---
title: Intercepteurs-EF Core
description: Interception pour les opérations de base de données et d’autres événements
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 459c0495e9a2f81e2e84388988f04ca9787080cc
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024223"
---
# <a name="interceptors"></a>Intercepteurs

Les intercepteurs de Entity Framework Core (EF Core) permettent l’interception, la modification et/ou la suppression d’opérations de EF Core. Cela comprend les opérations de base de données de bas niveau telles que l’exécution d’une commande, ainsi que les opérations de niveau supérieur, telles que les appels à SaveChanges.

Les intercepteurs diffèrent de la journalisation et des diagnostics en ce qu’ils autorisent la modification ou la suppression de l’opération qui est interceptée. La journalisation [simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) sont de meilleures options pour la journalisation.

Les intercepteurs sont inscrits par instance DbContext lorsque le contexte est configuré. Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.

## <a name="registering-interceptors"></a>Inscription des intercepteurs

Les intercepteurs sont inscrits à l’aide <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [de lors de la configuration d’une instance DbContext](xref:core/dbcontext-configuration/index). Cela se fait généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Par exemple :

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

Vous pouvez également `AddInterceptors` appeler dans le cadre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ou lors de la création d’une <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance à passer au constructeur DbContext.

> [!TIP]
> OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext. C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.

Les intercepteurs sont souvent sans État, ce qui signifie qu’une seule instance d’intercepteur peut être utilisée pour toutes les instances de DbContext. Par exemple :

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

Chaque instance d’intercepteur doit implémenter une ou plusieurs interfaces dérivées de <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> . Chaque instance doit être inscrite une seule fois, même si elle implémente plusieurs interfaces d’interception. EF Core acheminera les événements pour chaque interface comme il convient.

## <a name="database-interception"></a>Interception de base de données

> [!NOTE]
> L’interception de base de données a été introduite dans EF Core 3,0 et n’est disponible que pour les fournisseurs de bases de données relationnelles.
> La prise en charge des point de enregistrement a été introduite dans EF Core 5,0.

L’interception de base de données de bas niveau est divisée en trois interfaces, présentées dans le tableau suivant.

| Intercepteur                                                            | Opérations de base de données interceptées
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | Créer des commandes</br>Exécution de commandes</br>Échecs de commande</br>Suppression du DbDataReader de la commande
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | Ouverture et fermeture des connexions</br>Échecs de connexion
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | Création de transactions</br>Utilisation de transactions existantes</br>Validation des transactions</br>Restauration des transactions</br>Création et utilisation de points d’enregistrement</br>Échecs de transaction

Les classes de base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> et <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contiennent des implémentations de non-op pour chaque méthode dans l’interface correspondante. Utilisez les classes de base pour éviter d’avoir à implémenter des méthodes d’interception inutilisées.

Les méthodes sur chaque type d’intercepteur sont en paire, avec la première appelée avant le démarrage de l’opération de base de données, et la seconde après la fin de l’opération. Par exemple, Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> est appelé avant l’exécution d’une requête, et <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> est appelé après que la requête a été envoyée à la base de données.

Chaque paire de méthodes a des variantes synchrones et asynchrones. Cela permet d’effectuer des e/s asynchrones, telles que la demande d’un jeton d’accès, dans le cadre de l’interception d’une opération de base de données asynchrone.

### <a name="example-command-interception-to-add-query-hints"></a>Exemple : interception de commande pour ajouter des indicateurs de requête

> [!TIP]
> Vous pouvez [Télécharger l’exemple d’intercepteur de commande](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CommandInterception) à partir de github.

Un <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> peut être utilisé pour modifier SQL avant d’être envoyé à la base de données. Cet exemple montre comment modifier le SQL pour inclure un indicateur de requête.

Souvent, la partie la plus délicate de l’interception consiste à déterminer quand la commande correspond à la requête qui doit être modifiée. L’analyse de SQL est une option, mais elle a tendance à être fragile. Une autre option consiste à utiliser des [balises de requête EF Core](xref:core/querying/tags) pour baliser chaque requête qui doit être modifiée. Par exemple :

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

Cette balise peut ensuite être détectée dans l’intercepteur, car elle sera toujours incluse en tant que commentaire dans la première ligne du texte de la commande. Lors de la détection de la balise, la requête SQL est modifiée pour ajouter l’indicateur approprié :

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

Remarques :

* L’intercepteur hérite de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> pour éviter d’avoir à implémenter chaque méthode dans l’interface d’intercepteur.
* L’intercepteur implémente des méthodes synchrones et asynchrones. Cela permet de s’assurer que le même indicateur de requête est appliqué aux requêtes Sync et Async.
* L’intercepteur implémente les `Executing` méthodes qui sont appelées par EF Core avec le SQL généré _avant_ qu’il ne soit envoyé à la base de données. Comparez ceci avec les `Executed` méthodes, qui sont appelées après le retour de l’appel de base de données.

L’exécution du code dans cet exemple génère ce qui suit lorsqu’une requête est marquée :

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

En revanche, lorsqu’une requête n’est pas marquée, elle est envoyée à la base de données comme étant non modifiée :

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>Exemple : interception de connexion pour l’authentification SQL Azure à l’aide de ADD

> [!TIP]
> Vous pouvez [Télécharger l’exemple d’intercepteur de connexion](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/ConnectionInterception) à partir de github.

Un <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> peut être utilisé pour manipuler le <xref:System.Data.Common.DbConnection> avant qu’il ne soit utilisé pour se connecter à la base de données. Cela peut être utilisé pour obtenir un jeton d’accès Azure Active Directory (AAD). Par exemple :

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) prend désormais en charge l’authentification AAD via la chaîne de connexion. Consultez la rubrique <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> (éventuellement en anglais) pour plus d'informations.

> [!WARNING]
> Notez que l’intercepteur lève une exception si un appel de synchronisation est effectué pour ouvrir la connexion. Cela est dû au fait qu’il n’existe aucune méthode non asynchrone pour obtenir le jeton d’accès et qu’il n’existe [aucun moyen universel et simple d’appeler une méthode Async à partir d’un contexte non asynchrone sans risquer d’interblocage](https://devblogs.microsoft.com/dotnet/configureawait-faq/).

> [!WARNING]
> dans certains cas, le jeton d’accès ne peut pas être automatiquement mis en cache par le fournisseur de jetons Azure. Selon le type de jeton demandé, vous devrez peut-être implémenter votre propre mise en cache ici.

### <a name="example-advanced-command-interception-for-caching"></a>Exemple : interception de commande avancée pour la mise en cache

> [!TIP]
> Vous pouvez [Télécharger l’exemple d’intercepteur de commandes avancé](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) à partir de github.

Les intercepteurs de EF Core peuvent :

* Indiquer à EF Core de supprimer l’exécution de l’opération en cours d’interception
* Modifier le résultat de l’opération signalée à EF Core

Cet exemple montre un intercepteur qui utilise ces fonctionnalités pour se comporter comme un cache de second niveau primitif. Les résultats de requête mis en cache sont retournés pour une requête spécifique, évitant ainsi l’aller-retour d’une base de données.

> [!WARNING]
> Veillez à modifier le comportement par défaut EF Core de cette manière. EF Core peut se comporter de manière inattendue s’il obtient un résultat anormal qu’il ne peut pas traiter correctement. En outre, cet exemple illustre les concepts de l’intercepteur. Il n’est pas conçu comme un modèle pour une implémentation de cache de second niveau robuste.

Dans cet exemple, l’application exécute fréquemment une requête pour obtenir le « message quotidien » le plus récent :

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

Cette requête est [marquée](xref:core/querying/tags) pour pouvoir être facilement détectée dans l’intercepteur. L’idée est de n’interroger la base de données que pour obtenir un nouveau message une fois par jour. À d’autres moments, l’application utilisera un résultat mis en cache. (L’exemple utilise le délai de 10 secondes dans l’échantillon pour simuler un nouveau jour.)

#### <a name="interceptor-state"></a>État de l’intercepteur

Cet intercepteur est avec état : il stocke l’ID et le texte du message le plus récent quotidiennement interrogé, ainsi que l’heure à laquelle cette requête a été exécutée. En raison de cet État, nous avons également besoin d’un [verrou](/dotnet/csharp/language-reference/keywords/lock-statement) dans la mesure où la mise en cache nécessite que le même intercepteur soit utilisé par plusieurs instances de contexte.

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>Avant l'exécution

Dans la `Executing` méthode (par exemple, avant d’effectuer un appel de base de données), l’intercepteur détecte la requête avec balises, puis vérifie s’il existe un résultat mis en cache. Si un résultat de ce type est trouvé, la requête est supprimée et les résultats mis en cache sont utilisés à la place.

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

Notez que le code appelle <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> et passe un remplacement <xref:System.Data.Common.DbDataReader> contenant les données mises en cache. Ce InterceptionResult est ensuite retourné, provoquant la suppression de l’exécution de la requête. Le lecteur de remplacement est utilisé à la place par EF Core comme résultats de la requête.

Cet intercepteur manipule également le texte de la commande. Cette manipulation n’est pas obligatoire, mais elle améliore la clarté des messages de journal. Le texte de la commande n’a pas besoin d’être un SQL valide, car la requête n’est pas exécutée.

#### <a name="after-execution"></a>Après l’exécution

Si aucun message mis en cache n’est disponible, ou s’il a expiré, le code ci-dessus ne supprime pas le résultat. EF Core exécute donc la requête normalement. Elle retournera ensuite à la méthode de l’intercepteur `Executed` après l’exécution. À ce stade, si le résultat n’est pas déjà un lecteur mis en cache, l’ID et la chaîne du nouveau message sont exacts du lecteur réel et mis en cache pour la prochaine utilisation de cette requête.

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a>Démonstration

L' [exemple d’intercepteur de mise en cache](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/CachingInterception) contient une application console simple qui interroge les messages quotidiens pour tester la mise en cache :

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

Il s'ensuit la sortie suivante :

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

Notez que dans la sortie du journal, l’application continue à utiliser le message mis en cache jusqu’à l’expiration du délai d’attente. à ce moment-là, la base de données est à nouveau interrogée pour tout nouveau message.

## <a name="savechanges-interception"></a>Interception d’SaveChanges

> [!NOTE]
> L’interception d’SaveChanges a été introduite dans EF Core 5,0.

> [!TIP]
> Vous pouvez [Télécharger l’exemple d’intercepteur SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) à partir de github.

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>les points d’interception et sont définis par l' <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interface. Comme pour les autres intercepteurs, la <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> classe de base avec des méthodes sans op est fournie à titre de commodité.

> [!TIP]
> Les intercepteurs sont puissants. Toutefois, dans de nombreux cas, il peut être plus facile de remplacer la méthode SaveChanges ou [d’utiliser les événements .net pour SaveChanges](xref:core/logging-events-diagnostics/events) exposés sur DbContext.

### <a name="example-savechanges-interception-for-auditing"></a>Exemple : interception d’SaveChanges pour l’audit

SaveChanges peut être intercepté pour créer un enregistrement d’audit indépendant des modifications apportées.

> [!NOTE]
> Cela n’est pas destiné à être une solution d’audit fiable. Il s’agit plutôt d’un exemple simpliste utilisé pour illustrer les fonctionnalités d’interception.

#### <a name="the-application-context"></a>Contexte de l’application

L' [exemple d’audit](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) utilise un DbContext simple avec les blogs et les publications.

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

Notez qu’une nouvelle instance de l’intercepteur est inscrite pour chaque instance de DbContext. Cela est dû au fait que l’intercepteur d’audit contient l’État lié à l’instance de contexte actuelle.

#### <a name="the-audit-context"></a>Contexte d’audit

L’exemple contient également un deuxième DbContext et un modèle utilisés pour la base de données d’audit.

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a>L’intercepteur

L’idée générale de l’audit avec l’intercepteur est la suivante :

* Un message d’audit est créé au début de SaveChanges et écrit dans la base de données d’audit.
* SaveChanges est autorisé à continuer
* Si SaveChanges réussit, le message d’audit est mis à jour pour indiquer la réussite
* Si SaveChanges échoue, le message d’audit est mis à jour pour indiquer l’échec

La première étape est gérée avant l’envoi des modifications à la base de données à l’aide des remplacements de <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> .

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

La substitution des méthodes synchrones et asynchrones garantit que l’audit aura lieu indépendamment du fait que `SaveChanges` ou `SaveChangesAsync` soient appelés. Notez également que la surcharge Async est elle-même en mesure d’effectuer des e/s asynchrones non bloquantes dans la base de données d’audit. Vous pouvez effectuer une exception à partir de la `SavingChanges` méthode Sync pour vous assurer que toutes les e/s de base de données sont asynchrones. Cela nécessite ensuite que l’application appelle toujours `SaveChangesAsync` et jamais `SaveChanges` .

#### <a name="the-audit-message"></a>Message d’audit

Chaque méthode d’intercepteur a un `eventData` paramètre qui fournit des informations contextuelles sur l’événement qui est intercepté. Dans ce cas, le DbContext d’application actuel est inclus dans les données d’événement, qui est ensuite utilisé pour créer un message d’audit.

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

Le résultat est une `SaveChangesAudit` entité avec une collection d' `EntityAudit` entités, une pour chaque insertion, mise à jour ou suppression. L’intercepteur insère ensuite ces entités dans la base de données d’audit.

> [!TIP]
> ToString est substitué dans chaque classe de données d’événement EF Core pour générer le message de journal équivalent pour l’événement. Par exemple, l’appel de `ContextInitializedEventData.ToString` génère « Entity Framework Core 5.0.0 a initialisé «BlogsContext » à l’aide du fournisseur « Microsoft. EntityFrameworkCore. sqlite » avec options : None».

#### <a name="detecting-success"></a>Détection du succès

L’entité d’audit est stockée sur l’intercepteur afin de pouvoir y accéder à nouveau une fois que SaveChanges réussit ou échoue. En cas de réussite, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> ou <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> est appelée.

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

L’entité d’audit est attachée au contexte d’audit, car elle existe déjà dans la base de données et doit être mise à jour. Nous définissons ensuite `Succeeded` et `EndTime` , qui marque ces propriétés comme modifiées, si SaveChanges enverra une mise à jour à la base de données d’audit.

#### <a name="detecting-failure"></a>Détection de défaillance

L’échec est géré à peu près de la même façon que le succès, mais dans la <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> méthode ou. Les données d’événement contiennent l’exception qui a été levée.

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a>Démonstration

L' [exemple d’audit](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/SaveChangesInterception) contient une application console simple qui apporte des modifications à la base de données de création de blogs, puis affiche l’audit qui a été créé.

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

Le résultat affiche le contenu de la base de données d’audit :

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
