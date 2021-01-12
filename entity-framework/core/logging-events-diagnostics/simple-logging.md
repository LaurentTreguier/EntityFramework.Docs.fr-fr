---
title: Journalisation simple-EF Core
description: Journalisation à partir d’un DbContext de EF Core à l’aide de LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 5c2dc41122dfa3919d1e6a26b0760883d77ee1a0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129211"
---
# <a name="simple-logging"></a><span data-ttu-id="4346f-103">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="4346f-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="4346f-104">Cette fonctionnalité a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4346f-104">This feature was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="4346f-105">Vous pouvez [Télécharger l’exemple de cet article](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="4346f-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="4346f-106">Entity Framework Core (EF Core) la journalisation simple peut être utilisée pour obtenir facilement des journaux lors du développement et du débogage d’applications.</span><span class="sxs-lookup"><span data-stu-id="4346f-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="4346f-107">Cette forme de journalisation nécessite une configuration minimale et aucun package NuGet supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="4346f-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="4346f-108">EF Core s’intègre également à [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging), ce qui nécessite davantage de configuration, mais il est souvent plus approprié pour la journalisation dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="4346f-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="4346f-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="4346f-109">Configuration</span></span>

<span data-ttu-id="4346f-110">Les journaux EF Core sont accessibles à partir de n’importe quel type d’application par le biais de l’utilisation de lors de la <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> [configuration d’une instance DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4346f-110">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="4346f-111">Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4346f-111">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4346f-112">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-112">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="4346f-113">Vous pouvez également `LogTo` appeler dans le cadre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ou lors de la création d’une <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance à passer au `DbContext` constructeur.</span><span class="sxs-lookup"><span data-stu-id="4346f-113">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="4346f-114">OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="4346f-114">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="4346f-115">C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.</span><span class="sxs-lookup"><span data-stu-id="4346f-115">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="4346f-116">Diriger les journaux</span><span class="sxs-lookup"><span data-stu-id="4346f-116">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="4346f-117">Journalisation dans la console</span><span class="sxs-lookup"><span data-stu-id="4346f-117">Logging to the console</span></span>

<span data-ttu-id="4346f-118">`LogTo` requiert un <xref:System.Action%601> délégué qui accepte une chaîne.</span><span class="sxs-lookup"><span data-stu-id="4346f-118">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="4346f-119">EF Core appellera ce délégué avec une chaîne pour chaque message de journal généré.</span><span class="sxs-lookup"><span data-stu-id="4346f-119">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="4346f-120">C’est ensuite au délégué d’effectuer une opération avec le message donné.</span><span class="sxs-lookup"><span data-stu-id="4346f-120">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="4346f-121">La <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> méthode est souvent utilisée pour ce délégué, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4346f-121">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="4346f-122">Chaque message de journal est alors écrit dans la console.</span><span class="sxs-lookup"><span data-stu-id="4346f-122">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="4346f-123">Journalisation dans la fenêtre de débogage</span><span class="sxs-lookup"><span data-stu-id="4346f-123">Logging to the debug window</span></span>

<span data-ttu-id="4346f-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> peut être utilisé pour envoyer la sortie vers la fenêtre de débogage dans Visual Studio ou d’autres IDE.</span><span class="sxs-lookup"><span data-stu-id="4346f-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="4346f-125">La [syntaxe lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) doit être utilisée dans ce cas, car la `Debug` classe est compilée en dehors des versions release.</span><span class="sxs-lookup"><span data-stu-id="4346f-125">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="4346f-126">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-126">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="4346f-127">Journalisation dans un fichier</span><span class="sxs-lookup"><span data-stu-id="4346f-127">Logging to a file</span></span>

<span data-ttu-id="4346f-128">L’écriture dans un fichier requiert la création d’un <xref:System.IO.StreamWriter> ou similaire pour le fichier.</span><span class="sxs-lookup"><span data-stu-id="4346f-128">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="4346f-129">La <xref:System.IO.StreamWriter.WriteLine%2A> méthode peut ensuite être utilisée comme dans les autres exemples ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4346f-129">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="4346f-130">N’oubliez pas de vérifier que le fichier est fermé correctement en supprimant l’enregistreur lorsque le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="4346f-130">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="4346f-131">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-131">For example:</span></span>

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
> <span data-ttu-id="4346f-132">Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour la journalisation des fichiers dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="4346f-132">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="4346f-133">Obtention de messages détaillés</span><span class="sxs-lookup"><span data-stu-id="4346f-133">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="4346f-134">Données sensibles</span><span class="sxs-lookup"><span data-stu-id="4346f-134">Sensitive data</span></span>

<span data-ttu-id="4346f-135">Par défaut, EF Core n’inclut pas les valeurs des données dans les messages d’exception.</span><span class="sxs-lookup"><span data-stu-id="4346f-135">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="4346f-136">Cela est dû au fait que ces données peuvent être confidentielles et peuvent être révélées en production si une exception n’est pas gérée.</span><span class="sxs-lookup"><span data-stu-id="4346f-136">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="4346f-137">Toutefois, le fait de connaître les valeurs de données, en particulier pour les clés, peut être très utile lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="4346f-137">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="4346f-138">Vous pouvez l’activer dans EF Core en appelant <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="4346f-138">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="4346f-139">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-139">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="4346f-140">Exceptions de requête détaillées</span><span class="sxs-lookup"><span data-stu-id="4346f-140">Detailed query exceptions</span></span>

<span data-ttu-id="4346f-141">Pour des raisons de performances, EF Core n’encapsule pas chaque appel pour lire une valeur à partir du fournisseur de base de données dans un bloc try-catch.</span><span class="sxs-lookup"><span data-stu-id="4346f-141">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="4346f-142">Toutefois, cela entraîne parfois des exceptions difficiles à diagnostiquer, en particulier lorsque la base de données retourne une valeur NULL lorsqu’elle n’est pas autorisée par le modèle.</span><span class="sxs-lookup"><span data-stu-id="4346f-142">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="4346f-143">Si vous activez la fonction, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduira ces blocs try-catch et fournira des erreurs plus détaillées.</span><span class="sxs-lookup"><span data-stu-id="4346f-143">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="4346f-144">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-144">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="4346f-145">Filtrage</span><span class="sxs-lookup"><span data-stu-id="4346f-145">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="4346f-146">Niveaux de journalisation</span><span class="sxs-lookup"><span data-stu-id="4346f-146">Log levels</span></span>

<span data-ttu-id="4346f-147">Chaque message de journal EF Core est affecté à un niveau défini par l' <xref:Microsoft.Extensions.Logging.LogLevel> énumération.</span><span class="sxs-lookup"><span data-stu-id="4346f-147">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="4346f-148">Par défaut, EF Core la journalisation simple comprend chaque message au `Debug` niveau ou au-dessus.</span><span class="sxs-lookup"><span data-stu-id="4346f-148">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="4346f-149">`LogTo` un niveau minimum plus élevé peut être passé pour filtrer certains messages.</span><span class="sxs-lookup"><span data-stu-id="4346f-149">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="4346f-150">Par exemple, le passage de `Information` résultats dans un ensemble minimal de journaux limité à l’accès aux bases de données et à certains messages de maintenance.</span><span class="sxs-lookup"><span data-stu-id="4346f-150">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="4346f-151">Messages spécifiques</span><span class="sxs-lookup"><span data-stu-id="4346f-151">Specific messages</span></span>

<span data-ttu-id="4346f-152">Un est affecté à chaque message de journal <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="4346f-152">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="4346f-153">Ces ID sont accessibles à partir de la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe ou <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> de la classe pour les messages liés à des relations relationnelles.</span><span class="sxs-lookup"><span data-stu-id="4346f-153">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="4346f-154">Un fournisseur de base de données peut également avoir des ID spécifiques au fournisseur dans une classe similaire.</span><span class="sxs-lookup"><span data-stu-id="4346f-154">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="4346f-155">Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4346f-155">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="4346f-156">`LogTo` peut être configuré pour consigner uniquement les messages associés à un ou plusieurs ID d’événement.</span><span class="sxs-lookup"><span data-stu-id="4346f-156">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="4346f-157">Par exemple, pour enregistrer uniquement les messages pour le contexte en cours d’initialisation ou de suppression :</span><span class="sxs-lookup"><span data-stu-id="4346f-157">For example, to log only messages for the context being initialized or disposed:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="4346f-158">Catégories des messages</span><span class="sxs-lookup"><span data-stu-id="4346f-158">Message categories</span></span>

<span data-ttu-id="4346f-159">Chaque message de journal est affecté à une catégorie d’enregistreur d’événements hiérarchique nommée.</span><span class="sxs-lookup"><span data-stu-id="4346f-159">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="4346f-160">Les catégories sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="4346f-160">The categories are:</span></span>

| <span data-ttu-id="4346f-161">Category</span><span class="sxs-lookup"><span data-stu-id="4346f-161">Category</span></span>                                             | <span data-ttu-id="4346f-162">Messages</span><span class="sxs-lookup"><span data-stu-id="4346f-162">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="4346f-163">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4346f-163">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="4346f-164">Tous les messages de EF Core</span><span class="sxs-lookup"><span data-stu-id="4346f-164">All EF Core messages</span></span>
| <span data-ttu-id="4346f-165">Microsoft. EntityFrameworkCore. Database</span><span class="sxs-lookup"><span data-stu-id="4346f-165">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="4346f-166">Toutes les interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-166">All database interactions</span></span>
| <span data-ttu-id="4346f-167">Microsoft. EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="4346f-167">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="4346f-168">Utilisations d’une connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-168">Uses of a database connection</span></span>
| <span data-ttu-id="4346f-169">Microsoft. EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="4346f-169">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="4346f-170">Utilisations d’une commande de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-170">Uses of a database command</span></span>
| <span data-ttu-id="4346f-171">Microsoft. EntityFrameworkCore. Database. transaction</span><span class="sxs-lookup"><span data-stu-id="4346f-171">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="4346f-172">Utilisations d’une transaction de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-172">Uses of a database transaction</span></span>
| <span data-ttu-id="4346f-173">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="4346f-173">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="4346f-174">Enregistrement d’entités, à l’exception des interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-174">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="4346f-175">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="4346f-175">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="4346f-176">Toutes les interactions entre le modèle et les métadonnées</span><span class="sxs-lookup"><span data-stu-id="4346f-176">All model and metadata interactions</span></span>
| <span data-ttu-id="4346f-177">Microsoft. EntityFrameworkCore. Model. validation</span><span class="sxs-lookup"><span data-stu-id="4346f-177">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="4346f-178">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="4346f-178">Model validation</span></span>
| <span data-ttu-id="4346f-179">Microsoft. EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="4346f-179">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="4346f-180">Requêtes, à l’exception des interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-180">Queries, excluding database interactions</span></span>
| <span data-ttu-id="4346f-181">Microsoft. EntityFrameworkCore. infrastructure</span><span class="sxs-lookup"><span data-stu-id="4346f-181">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="4346f-182">Événements généraux, tels que la création de contexte</span><span class="sxs-lookup"><span data-stu-id="4346f-182">General events, such as context creation</span></span>
| <span data-ttu-id="4346f-183">Microsoft. EntityFrameworkCore. échafaudage</span><span class="sxs-lookup"><span data-stu-id="4346f-183">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="4346f-184">Ingénierie inverse de la base de données</span><span class="sxs-lookup"><span data-stu-id="4346f-184">Database reverse engineering</span></span>
| <span data-ttu-id="4346f-185">Microsoft. EntityFrameworkCore. migrations</span><span class="sxs-lookup"><span data-stu-id="4346f-185">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="4346f-186">Migrations</span><span class="sxs-lookup"><span data-stu-id="4346f-186">Migrations</span></span>
| <span data-ttu-id="4346f-187">Microsoft. EntityFrameworkCore. suivi</span><span class="sxs-lookup"><span data-stu-id="4346f-187">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="4346f-188">Interactions de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="4346f-188">Change tracking interactions</span></span>

<span data-ttu-id="4346f-189">`LogTo` peut être configuré pour enregistrer uniquement les messages d’une ou plusieurs catégories.</span><span class="sxs-lookup"><span data-stu-id="4346f-189">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="4346f-190">Par exemple, pour enregistrer uniquement les interactions entre les bases de données :</span><span class="sxs-lookup"><span data-stu-id="4346f-190">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="4346f-191">Notez que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fournit une API hiérarchique pour la recherche d’une catégorie et évite d’avoir à coder en dur des chaînes.</span><span class="sxs-lookup"><span data-stu-id="4346f-191">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="4346f-192">Étant donné que les catégories sont hiérarchiques, cet exemple qui utilise la `Database` catégorie inclut tous les messages des sous-catégories `Database.Connection` , `Database.Command` et `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="4346f-192">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="4346f-193">Filtres personnalisés</span><span class="sxs-lookup"><span data-stu-id="4346f-193">Custom filters</span></span>

<span data-ttu-id="4346f-194">`LogTo` permet d’utiliser un filtre personnalisé pour les cas où aucune des options de filtrage ci-dessus n’est suffisante.</span><span class="sxs-lookup"><span data-stu-id="4346f-194">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="4346f-195">Par exemple, pour enregistrer un message au niveau `Information` ou au-dessus, ainsi que des messages pour l’ouverture et la fermeture d’une connexion :</span><span class="sxs-lookup"><span data-stu-id="4346f-195">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

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
> <span data-ttu-id="4346f-196">Le filtrage à l’aide de filtres personnalisés ou de l’une des autres options présentées ici est plus efficace que le filtrage dans le `LogTo` délégué.</span><span class="sxs-lookup"><span data-stu-id="4346f-196">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the `LogTo` delegate.</span></span> <span data-ttu-id="4346f-197">En effet, si le filtre détermine que le message ne doit pas être consigné, le message du journal n’est même pas créé.</span><span class="sxs-lookup"><span data-stu-id="4346f-197">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="4346f-198">Configuration de messages spécifiques</span><span class="sxs-lookup"><span data-stu-id="4346f-198">Configuration for specific messages</span></span>

<span data-ttu-id="4346f-199">L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF Core permet aux applications de modifier ce qui se produit lorsqu’un événement spécifique est rencontré.</span><span class="sxs-lookup"><span data-stu-id="4346f-199">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="4346f-200">Cela peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4346f-200">This can be used to:</span></span>

* <span data-ttu-id="4346f-201">Modifier le niveau de journal auquel l’événement est enregistré</span><span class="sxs-lookup"><span data-stu-id="4346f-201">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="4346f-202">Ignorer l’enregistrement de l’événement</span><span class="sxs-lookup"><span data-stu-id="4346f-202">Skip logging the event altogether</span></span>
* <span data-ttu-id="4346f-203">Lever une exception lorsque l’événement se produit</span><span class="sxs-lookup"><span data-stu-id="4346f-203">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="4346f-204">Modification du niveau de journalisation d’un événement</span><span class="sxs-lookup"><span data-stu-id="4346f-204">Changing the log level for an event</span></span>

<span data-ttu-id="4346f-205">L’exemple précédent utilisait un filtre personnalisé pour enregistrer chaque message dans `LogLevel.Information` , ainsi que deux événements définis pour `LogLevel.Debug` .</span><span class="sxs-lookup"><span data-stu-id="4346f-205">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="4346f-206">Le même résultat peut être obtenu en modifiant le niveau de journal des deux `Debug` événements sur `Information` :</span><span class="sxs-lookup"><span data-stu-id="4346f-206">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="4346f-207">Supprimer la journalisation d’un événement</span><span class="sxs-lookup"><span data-stu-id="4346f-207">Suppress logging an event</span></span>

<span data-ttu-id="4346f-208">De la même façon, un événement individuel peut être supprimé de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="4346f-208">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="4346f-209">Cela est particulièrement utile pour ignorer un avertissement qui a été révisé et compris.</span><span class="sxs-lookup"><span data-stu-id="4346f-209">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="4346f-210">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-210">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="4346f-211">Lever pour un événement</span><span class="sxs-lookup"><span data-stu-id="4346f-211">Throw for an event</span></span>

<span data-ttu-id="4346f-212">Enfin, EF Core peut être configuré pour lever une exception pour un événement donné.</span><span class="sxs-lookup"><span data-stu-id="4346f-212">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="4346f-213">Cela s’avère particulièrement utile pour modifier un avertissement en erreur.</span><span class="sxs-lookup"><span data-stu-id="4346f-213">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="4346f-214">(En effet, c’était l’objectif initial de la `ConfigureWarnings` méthode, donc le nom.) Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-214">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="4346f-215">Contenu et mise en forme du message</span><span class="sxs-lookup"><span data-stu-id="4346f-215">Message contents and formatting</span></span>

<span data-ttu-id="4346f-216">Le contenu par défaut de `LogTo` est mis en forme sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="4346f-216">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="4346f-217">La première ligne contient les métadonnées du message :</span><span class="sxs-lookup"><span data-stu-id="4346f-217">The first line contains message metadata:</span></span>

* <span data-ttu-id="4346f-218"><xref:Microsoft.Extensions.Logging.LogLevel>En tant que préfixe à quatre caractères</span><span class="sxs-lookup"><span data-stu-id="4346f-218">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="4346f-219">Une estampille locale, mise en forme pour la culture actuelle</span><span class="sxs-lookup"><span data-stu-id="4346f-219">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="4346f-220"><xref:Microsoft.Extensions.Logging.EventId>Dans le formulaire qui peut être copié/collé pour récupérer le membre de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> ou de l’une des autres `EventId` classes, plus la valeur d’ID brute</span><span class="sxs-lookup"><span data-stu-id="4346f-220">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="4346f-221">Catégorie d’événement, comme décrit ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4346f-221">The event category, as described above.</span></span>

<span data-ttu-id="4346f-222">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-222">For example:</span></span>

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

<span data-ttu-id="4346f-223">Ce contenu peut être personnalisé en passant des valeurs à partir de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> , comme indiqué dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="4346f-223">This content can be customized by passing values from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions>, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="4346f-224">Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour davantage de contrôle sur la mise en forme des journaux.</span><span class="sxs-lookup"><span data-stu-id="4346f-224">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="4346f-225">Utilisation de l’heure UTC</span><span class="sxs-lookup"><span data-stu-id="4346f-225">Using UTC time</span></span>

<span data-ttu-id="4346f-226">Par défaut, les horodateurs sont conçus pour la consommation locale pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="4346f-226">By default, timestamps are designed for local consumption while debugging.</span></span> <span data-ttu-id="4346f-227">Utilisez <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> plutôt pour utiliser des horodatages UTC indépendantes de la culture, mais conservez tout le reste.</span><span class="sxs-lookup"><span data-stu-id="4346f-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="4346f-228">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-228">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="4346f-229">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="4346f-229">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="4346f-230">Journalisation sur une seule ligne</span><span class="sxs-lookup"><span data-stu-id="4346f-230">Single line logging</span></span>

<span data-ttu-id="4346f-231">Il est parfois utile d’avoir une seule ligne par message de journal.</span><span class="sxs-lookup"><span data-stu-id="4346f-231">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="4346f-232">Cette option peut être activée par <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4346f-232">This can be enabled by <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4346f-233">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-233">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="4346f-234">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="4346f-234">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="4346f-235">Autres options de contenu</span><span class="sxs-lookup"><span data-stu-id="4346f-235">Other content options</span></span>

<span data-ttu-id="4346f-236">D’autres indicateurs dans <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> peuvent être utilisés pour réduire la quantité de métadonnées incluses dans le journal.</span><span class="sxs-lookup"><span data-stu-id="4346f-236">Other flags in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="4346f-237">Cela peut être utile conjointement avec la journalisation sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="4346f-237">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="4346f-238">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-238">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="4346f-239">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="4346f-239">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="4346f-240">Déplacement à partir de EF6</span><span class="sxs-lookup"><span data-stu-id="4346f-240">Moving from EF6</span></span>

<span data-ttu-id="4346f-241">EF Core la journalisation simple diffère de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6 de deux façons importantes :</span><span class="sxs-lookup"><span data-stu-id="4346f-241">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="4346f-242">Les messages de journal ne sont pas limités aux interactions de base de données uniquement</span><span class="sxs-lookup"><span data-stu-id="4346f-242">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="4346f-243">La journalisation doit être configurée au moment de l’initialisation du contexte</span><span class="sxs-lookup"><span data-stu-id="4346f-243">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="4346f-244">Pour la première différence, le filtrage décrit ci-dessus peut être utilisé pour limiter les messages à enregistrer.</span><span class="sxs-lookup"><span data-stu-id="4346f-244">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="4346f-245">La deuxième différence est une modification intentionnelle pour améliorer les performances en ne générant pas de messages de journal lorsqu’ils ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="4346f-245">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="4346f-246">Toutefois, il est toujours possible d’obtenir un comportement similaire à EF6 en créant une `Log` propriété sur votre `DbContext` , puis en l’utilisant uniquement lorsqu’elle a été définie.</span><span class="sxs-lookup"><span data-stu-id="4346f-246">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="4346f-247">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4346f-247">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
