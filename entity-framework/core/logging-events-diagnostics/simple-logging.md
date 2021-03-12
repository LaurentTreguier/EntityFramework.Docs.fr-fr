---
title: Journalisation simple-EF Core
description: Journalisation à partir d’un DbContext de EF Core à l’aide de LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 24a3de668a0e587dcc0ab2ae5f061b0eb4d4edcf
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024210"
---
# <a name="simple-logging"></a>Journalisation simple

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0.

> [!TIP]
> Vous pouvez [Télécharger l’exemple de cet article](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Logging/SimpleLogging) à partir de github.

Entity Framework Core (EF Core) la journalisation simple peut être utilisée pour obtenir facilement des journaux lors du développement et du débogage d’applications. Cette forme de journalisation nécessite une configuration minimale et aucun package NuGet supplémentaire.

> [!TIP]
> EF Core s’intègre également à [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), ce qui nécessite davantage de configuration, mais il est souvent plus approprié pour la journalisation dans les applications de production.

## <a name="configuration"></a>Configuration

Les journaux EF Core sont accessibles à partir de n’importe quel type d’application par le biais de l’utilisation de lors de la <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> [configuration d’une instance DbContext](xref:core/dbcontext-configuration/index). Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Vous pouvez également `LogTo` appeler dans le cadre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ou lors de la création d’une <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance à passer au `DbContext` constructeur.

> [!TIP]
> OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext. C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.

## <a name="directing-the-logs"></a>Diriger les journaux

### <a name="logging-to-the-console"></a>Journalisation dans la console

`LogTo` requiert un <xref:System.Action%601> délégué qui accepte une chaîne. EF Core appellera ce délégué avec une chaîne pour chaque message de journal généré. C’est ensuite au délégué d’effectuer une opération avec le message donné.

La <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> méthode est souvent utilisée pour ce délégué, comme indiqué ci-dessus. Chaque message de journal est alors écrit dans la console.

### <a name="logging-to-the-debug-window"></a>Journalisation dans la fenêtre de débogage

<xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> peut être utilisé pour envoyer la sortie vers la fenêtre de débogage dans Visual Studio ou d’autres IDE. La [syntaxe lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) doit être utilisée dans ce cas, car la `Debug` classe est compilée en dehors des versions release. Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a>Journalisation dans un fichier

L’écriture dans un fichier requiert la création d’un <xref:System.IO.StreamWriter> ou similaire pour le fichier. La <xref:System.IO.StreamWriter.WriteLine%2A> méthode peut ensuite être utilisée comme dans les autres exemples ci-dessus. N’oubliez pas de vérifier que le fichier est fermé correctement en supprimant l’enregistreur lorsque le contexte est supprimé. Par exemple :

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true);

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }

    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour la journalisation des fichiers dans les applications de production.

## <a name="getting-detailed-messages"></a>Obtention de messages détaillés

### <a name="sensitive-data"></a>Données sensibles

Par défaut, EF Core n’inclut pas les valeurs des données dans les messages d’exception. Cela est dû au fait que ces données peuvent être confidentielles et peuvent être révélées en production si une exception n’est pas gérée.

Toutefois, le fait de connaître les valeurs de données, en particulier pour les clés, peut être très utile lors du débogage. Vous pouvez l’activer dans EF Core en appelant <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Exceptions de requête détaillées

Pour des raisons de performances, EF Core n’encapsule pas chaque appel pour lire une valeur à partir du fournisseur de base de données dans un bloc try-catch. Toutefois, cela entraîne parfois des exceptions difficiles à diagnostiquer, en particulier lorsque la base de données retourne une valeur NULL lorsqu’elle n’est pas autorisée par le modèle.

Si vous activez la fonction, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduira ces blocs try-catch et fournira des erreurs plus détaillées. Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a>Filtrage

### <a name="log-levels"></a>Niveaux de journalisation

Chaque message de journal EF Core est affecté à un niveau défini par l' <xref:Microsoft.Extensions.Logging.LogLevel> énumération. Par défaut, EF Core la journalisation simple comprend chaque message au `Debug` niveau ou au-dessus. `LogTo` un niveau minimum plus élevé peut être passé pour filtrer certains messages. Par exemple, le passage de `Information` résultats dans un ensemble minimal de journaux limité à l’accès aux bases de données et à certains messages de maintenance.

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a>Messages spécifiques

Un est affecté à chaque message de journal <xref:Microsoft.Extensions.Logging.EventId> . Ces ID sont accessibles à partir de la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe ou <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> de la classe pour les messages liés à des relations relationnelles. Un fournisseur de base de données peut également avoir des ID spécifiques au fournisseur dans une classe similaire. Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.

`LogTo` peut être configuré pour consigner uniquement les messages associés à un ou plusieurs ID d’événement. Par exemple, pour enregistrer uniquement les messages pour le contexte en cours d’initialisation ou de suppression :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a>Catégories des messages

Chaque message de journal est affecté à une catégorie d’enregistreur d’événements hiérarchique nommée. Les catégories sont les suivantes :

| Category                                             | Messages
|:-----------------------------------------------------|-------------------------------------------------
| Microsoft.EntityFrameworkCore                        | Tous les messages de EF Core
| Microsoft. EntityFrameworkCore. Database               | Toutes les interactions de base de données
| Microsoft. EntityFrameworkCore. Database. Connection    | Utilisations d’une connexion de base de données
| Microsoft. EntityFrameworkCore. Database. Command       | Utilisations d’une commande de base de données
| Microsoft. EntityFrameworkCore. Database. transaction   | Utilisations d’une transaction de base de données
| Microsoft. EntityFrameworkCore. Update                 | Enregistrement d’entités, à l’exception des interactions de base de données
| Microsoft. EntityFrameworkCore. Model                  | Toutes les interactions entre le modèle et les métadonnées
| Microsoft. EntityFrameworkCore. Model. validation       | Validation du modèle
| Microsoft. EntityFrameworkCore. Query                  | Requêtes, à l’exception des interactions de base de données
| Microsoft. EntityFrameworkCore. infrastructure         | Événements généraux, tels que la création de contexte
| Microsoft. EntityFrameworkCore. échafaudage            | Ingénierie inverse de la base de données
| Microsoft. EntityFrameworkCore. migrations             | Migrations
| Microsoft. EntityFrameworkCore. suivi         | Interactions de suivi des modifications

`LogTo` peut être configuré pour enregistrer uniquement les messages d’une ou plusieurs catégories. Par exemple, pour enregistrer uniquement les interactions entre les bases de données :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

Notez que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fournit une API hiérarchique pour la recherche d’une catégorie et évite d’avoir à coder en dur des chaînes.

Étant donné que les catégories sont hiérarchiques, cet exemple qui utilise la `Database` catégorie inclut tous les messages des sous-catégories `Database.Connection` , `Database.Command` et `Database.Transaction` .

### <a name="custom-filters"></a>Filtres personnalisés

`LogTo` permet d’utiliser un filtre personnalisé pour les cas où aucune des options de filtrage ci-dessus n’est suffisante. Par exemple, pour enregistrer un message au niveau `Information` ou au-dessus, ainsi que des messages pour l’ouverture et la fermeture d’une connexion :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> Le filtrage à l’aide de filtres personnalisés ou de l’une des autres options présentées ici est plus efficace que le filtrage dans le `LogTo` délégué. En effet, si le filtre détermine que le message ne doit pas être consigné, le message du journal n’est même pas créé.

## <a name="configuration-for-specific-messages"></a>Configuration de messages spécifiques

L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF Core permet aux applications de modifier ce qui se produit lorsqu’un événement spécifique est rencontré. Cela peut être utilisé pour :

* Modifier le niveau de journal auquel l’événement est enregistré
* Ignorer l’enregistrement de l’événement
* Lever une exception lorsque l’événement se produit

### <a name="changing-the-log-level-for-an-event"></a>Modification du niveau de journalisation d’un événement

L’exemple précédent utilisait un filtre personnalisé pour enregistrer chaque message dans `LogLevel.Information` , ainsi que deux événements définis pour `LogLevel.Debug` . Le même résultat peut être obtenu en modifiant le niveau de journal des deux `Debug` événements sur `Information` :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Supprimer la journalisation d’un événement

De la même façon, un événement individuel peut être supprimé de la journalisation. Cela est particulièrement utile pour ignorer un avertissement qui a été révisé et compris. Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Lever pour un événement

Enfin, EF Core peut être configuré pour lever une exception pour un événement donné. Cela s’avère particulièrement utile pour modifier un avertissement en erreur. (En effet, c’était l’objectif initial de la `ConfigureWarnings` méthode, donc le nom.) Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a>Contenu et mise en forme du message

Le contenu par défaut de `LogTo` est mis en forme sur plusieurs lignes. La première ligne contient les métadonnées du message :

* <xref:Microsoft.Extensions.Logging.LogLevel>En tant que préfixe à quatre caractères
* Une estampille locale, mise en forme pour la culture actuelle
* <xref:Microsoft.Extensions.Logging.EventId>Dans le formulaire qui peut être copié/collé pour récupérer le membre de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> ou de l’une des autres `EventId` classes, plus la valeur d’ID brute
* Catégorie d’événement, comme décrit ci-dessus.

Par exemple :

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

Ce contenu peut être personnalisé en passant des valeurs à partir de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , comme indiqué dans les sections suivantes.

> [!TIP]
> Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour davantage de contrôle sur la mise en forme des journaux.

### <a name="using-utc-time"></a>Utilisation de l’heure UTC

Par défaut, les horodateurs sont conçus pour la consommation locale pendant le débogage. Utilisez <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> plutôt pour utiliser des horodatages UTC indépendantes de la culture, mais conservez tout le reste. Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

Cet exemple entraîne la mise en forme suivante du journal :

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a>Journalisation sur une seule ligne

Il est parfois utile d’avoir une seule ligne par message de journal. Cette option peut être activée par <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> . Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

Cet exemple entraîne la mise en forme suivante du journal :

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a>Autres options de contenu

D’autres indicateurs dans <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> peuvent être utilisés pour réduire la quantité de métadonnées incluses dans le journal. Cela peut être utile conjointement avec la journalisation sur une seule ligne. Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

Cet exemple entraîne la mise en forme suivante du journal :

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a>Déplacement à partir de EF6

EF Core la journalisation simple diffère de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6 de deux façons importantes :

* Les messages de journal ne sont pas limités aux interactions de base de données uniquement
* La journalisation doit être configurée au moment de l’initialisation du contexte

Pour la première différence, le filtrage décrit ci-dessus peut être utilisé pour limiter les messages à enregistrer.

La deuxième différence est une modification intentionnelle pour améliorer les performances en ne générant pas de messages de journal lorsqu’ils ne sont pas nécessaires. Toutefois, il est toujours possible d’obtenir un comportement similaire à EF6 en créant une `Log` propriété sur votre `DbContext` , puis en l’utilisant uniquement lorsqu’elle a été définie. Par exemple :

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
