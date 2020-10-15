---
title: Journalisation simple-EF Core
description: Journalisation à partir d’un DbContext EFCore à l’aide de LogTo
author: ajcvickers
ms.date: 10/03/2020
uid: core/miscellaneous/events/simple-logging
ms.openlocfilehash: 2d671dd9daad16a6431b91419736ec67733e41b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066611"
---
# <a name="simple-logging"></a><span data-ttu-id="6178c-103">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="6178c-103">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="6178c-104">Cette fonctionnalité a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="6178c-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="6178c-105">Vous pouvez [afficher et télécharger l’exemple de cet article](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="6178c-105">You can [view and download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) on GitHub.</span></span>

<span data-ttu-id="6178c-106">Entity Framework Core (EF Core) la journalisation simple peut être utilisée pour obtenir facilement des journaux lors du développement et du débogage d’applications.</span><span class="sxs-lookup"><span data-stu-id="6178c-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="6178c-107">Cette forme de journalisation nécessite une configuration minimale et aucun package NuGet supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="6178c-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="6178c-108">EF Core s’intègre également à [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging), ce qui nécessite davantage de configuration, mais il est souvent plus approprié pour la journalisation dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="6178c-108">EF Core also integrates with [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="6178c-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="6178c-109">Configuration</span></span>

<span data-ttu-id="6178c-110">EF Core les journaux sont accessibles à partir de n’importe quel type d’application via l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="6178c-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="6178c-111">lors [de la configuration d’une instance DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6178c-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="6178c-112">Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6178c-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6178c-113">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="6178c-114">Vous pouvez également `LogTo` appeler dans le cadre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ou lors de la création d’une <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance à passer au `DbContext` constructeur.</span><span class="sxs-lookup"><span data-stu-id="6178c-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="6178c-115">OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="6178c-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="6178c-116">C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.</span><span class="sxs-lookup"><span data-stu-id="6178c-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="6178c-117">Diriger les journaux</span><span class="sxs-lookup"><span data-stu-id="6178c-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="6178c-118">Journalisation dans la console</span><span class="sxs-lookup"><span data-stu-id="6178c-118">Logging to the console</span></span>

<span data-ttu-id="6178c-119">`LogTo` requiert un <xref:System.Action%601> délégué qui accepte une chaîne.</span><span class="sxs-lookup"><span data-stu-id="6178c-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="6178c-120">EF Core appellera ce délégué avec une chaîne pour chaque message de journal généré.</span><span class="sxs-lookup"><span data-stu-id="6178c-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="6178c-121">C’est ensuite au délégué d’effectuer une opération avec le message donné.</span><span class="sxs-lookup"><span data-stu-id="6178c-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="6178c-122">La <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> méthode est souvent utilisée pour ce délégué, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6178c-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="6178c-123">Chaque message de journal est alors écrit dans la console.</span><span class="sxs-lookup"><span data-stu-id="6178c-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="6178c-124">Journalisation dans la fenêtre de débogage</span><span class="sxs-lookup"><span data-stu-id="6178c-124">Logging to the debug window</span></span>

<span data-ttu-id="6178c-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> peut être utilisé pour envoyer la sortie vers la fenêtre de débogage dans Visual Studio ou d’autres IDE.</span><span class="sxs-lookup"><span data-stu-id="6178c-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="6178c-126">La [syntaxe lambda](/dotnet/csharp/language-reference/operators/lambda-expressions) doit être utilisée dans ce cas, car la `Debug` classe est compilée en dehors des versions release.</span><span class="sxs-lookup"><span data-stu-id="6178c-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="6178c-127">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="6178c-128">Journalisation dans un fichier</span><span class="sxs-lookup"><span data-stu-id="6178c-128">Logging to a file</span></span>

<span data-ttu-id="6178c-129">L’écriture dans un fichier requiert la création d’un <xref:System.IO.StreamWriter> ou similaire pour le fichier.</span><span class="sxs-lookup"><span data-stu-id="6178c-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="6178c-130">La <xref:System.IO.StreamWriter.WriteLine%2A> méthode peut ensuite être utilisée comme dans les autres exemples ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6178c-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="6178c-131">N’oubliez pas de vérifier que le fichier est fermé correctement en supprimant l’enregistreur lorsque le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="6178c-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="6178c-132">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-132">For example:</span></span>

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
[!code-csharp[LogToFile](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="6178c-133">Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour la journalisation des fichiers dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="6178c-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="6178c-134">Obtention de messages détaillés</span><span class="sxs-lookup"><span data-stu-id="6178c-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="6178c-135">Données sensibles</span><span class="sxs-lookup"><span data-stu-id="6178c-135">Sensitive data</span></span>

<span data-ttu-id="6178c-136">Par défaut, EF Core n’inclut pas les valeurs des données dans les messages d’exception.</span><span class="sxs-lookup"><span data-stu-id="6178c-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="6178c-137">Cela est dû au fait que ces données peuvent être confidentielles et peuvent être révélées en production si une exception n’est pas gérée.</span><span class="sxs-lookup"><span data-stu-id="6178c-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="6178c-138">Toutefois, le fait de connaître les valeurs de données, en particulier pour les clés, peut être très utile lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="6178c-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="6178c-139">Vous pouvez l’activer dans EF Core en appelant <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="6178c-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="6178c-140">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="6178c-141">Exceptions de requête détaillées</span><span class="sxs-lookup"><span data-stu-id="6178c-141">Detailed query exceptions</span></span>

<span data-ttu-id="6178c-142">Pour des raisons de performances, EF Core n’encapsule pas chaque appel pour lire une valeur à partir du fournisseur de base de données dans un bloc try-catch.</span><span class="sxs-lookup"><span data-stu-id="6178c-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="6178c-143">Toutefois, cela entraîne parfois des exceptions difficiles à diagnostiquer, en particulier lorsque la base de données retourne une valeur NULL lorsqu’elle n’est pas autorisée par le modèle.</span><span class="sxs-lookup"><span data-stu-id="6178c-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="6178c-144">Si vous activez la fonction, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduira ces blocs try-catch et fournira des erreurs plus détaillées.</span><span class="sxs-lookup"><span data-stu-id="6178c-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="6178c-145">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="6178c-146">Filtrage</span><span class="sxs-lookup"><span data-stu-id="6178c-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="6178c-147">Niveaux de journal</span><span class="sxs-lookup"><span data-stu-id="6178c-147">Log levels</span></span>

<span data-ttu-id="6178c-148">Chaque message de journal EF Core est affecté à un niveau défini par l' <xref:Microsoft.Extensions.Logging.LogLevel> énumération.</span><span class="sxs-lookup"><span data-stu-id="6178c-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="6178c-149">Par défaut, EF Core la journalisation simple comprend chaque message au `Debug` niveau ou au-dessus.</span><span class="sxs-lookup"><span data-stu-id="6178c-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="6178c-150">`LogTo` un niveau minimum plus élevé peut être passé pour filtrer certains messages.</span><span class="sxs-lookup"><span data-stu-id="6178c-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="6178c-151">Par exemple, le passage de `Information` résultats dans un ensemble minimal de journaux limité à l’accès aux bases de données et à certains messages de maintenance.</span><span class="sxs-lookup"><span data-stu-id="6178c-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="6178c-152">Messages spécifiques</span><span class="sxs-lookup"><span data-stu-id="6178c-152">Specific messages</span></span>

<span data-ttu-id="6178c-153">Un est affecté à chaque message de journal <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="6178c-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="6178c-154">Ces ID sont accessibles à partir de la <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> classe ou <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> de la classe pour les messages liés à des relations relationnelles.</span><span class="sxs-lookup"><span data-stu-id="6178c-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="6178c-155">Un fournisseur de base de données peut également avoir des ID spécifiques au fournisseur dans une classe similaire.</span><span class="sxs-lookup"><span data-stu-id="6178c-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="6178c-156">Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6178c-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="6178c-157">`LogTo` peut être configuré pour consigner uniquement les messages associés à un ou plusieurs ID d’événement.</span><span class="sxs-lookup"><span data-stu-id="6178c-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="6178c-158">Par exemple, pour enregistrer uniquement les messages pour le contexte en cours d’initialisation ou de suppression :</span><span class="sxs-lookup"><span data-stu-id="6178c-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="6178c-159">Catégories des messages</span><span class="sxs-lookup"><span data-stu-id="6178c-159">Message categories</span></span>

<span data-ttu-id="6178c-160">Chaque message de journal est affecté à une catégorie d’enregistreur d’événements hiérarchique nommée.</span><span class="sxs-lookup"><span data-stu-id="6178c-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="6178c-161">Les catégories sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="6178c-161">The categories are:</span></span>

| <span data-ttu-id="6178c-162">Category</span><span class="sxs-lookup"><span data-stu-id="6178c-162">Category</span></span>                                             | <span data-ttu-id="6178c-163">Messages</span><span class="sxs-lookup"><span data-stu-id="6178c-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="6178c-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="6178c-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="6178c-165">Tous les messages de EF Core</span><span class="sxs-lookup"><span data-stu-id="6178c-165">All EF Core messages</span></span>
| <span data-ttu-id="6178c-166">Microsoft. EntityFrameworkCore. Database</span><span class="sxs-lookup"><span data-stu-id="6178c-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="6178c-167">Toutes les interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-167">All database interactions</span></span>
| <span data-ttu-id="6178c-168">Microsoft. EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="6178c-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="6178c-169">Utilisations d’une connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-169">Uses of a database connection</span></span>
| <span data-ttu-id="6178c-170">Microsoft. EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="6178c-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="6178c-171">Utilisations d’une commande de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-171">Uses of a database command</span></span>
| <span data-ttu-id="6178c-172">Microsoft. EntityFrameworkCore. Database. transaction</span><span class="sxs-lookup"><span data-stu-id="6178c-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="6178c-173">Utilisations d’une transaction de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-173">Uses of a database transaction</span></span>
| <span data-ttu-id="6178c-174">Microsoft. EntityFrameworkCore. Update</span><span class="sxs-lookup"><span data-stu-id="6178c-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="6178c-175">Enregistrement d’entités, à l’exception des interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="6178c-176">Microsoft. EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="6178c-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="6178c-177">Toutes les interactions entre le modèle et les métadonnées</span><span class="sxs-lookup"><span data-stu-id="6178c-177">All model and metadata interactions</span></span>
| <span data-ttu-id="6178c-178">Microsoft. EntityFrameworkCore. Model. validation</span><span class="sxs-lookup"><span data-stu-id="6178c-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="6178c-179">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="6178c-179">Model validation</span></span>
| <span data-ttu-id="6178c-180">Microsoft. EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="6178c-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="6178c-181">Requêtes, à l’exception des interactions de base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="6178c-182">Microsoft. EntityFrameworkCore. infrastructure</span><span class="sxs-lookup"><span data-stu-id="6178c-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="6178c-183">Événements généraux, tels que la création de contexte</span><span class="sxs-lookup"><span data-stu-id="6178c-183">General events, such as context creation</span></span>
| <span data-ttu-id="6178c-184">Microsoft. EntityFrameworkCore. échafaudage</span><span class="sxs-lookup"><span data-stu-id="6178c-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="6178c-185">Ingénierie inverse de la base de données</span><span class="sxs-lookup"><span data-stu-id="6178c-185">Database reverse engineering</span></span>
| <span data-ttu-id="6178c-186">Microsoft. EntityFrameworkCore. migrations</span><span class="sxs-lookup"><span data-stu-id="6178c-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="6178c-187">Migrations</span><span class="sxs-lookup"><span data-stu-id="6178c-187">Migrations</span></span>
| <span data-ttu-id="6178c-188">Microsoft. EntityFrameworkCore. suivi</span><span class="sxs-lookup"><span data-stu-id="6178c-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="6178c-189">Interactions de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="6178c-189">Change tracking interactions</span></span>

<span data-ttu-id="6178c-190">`LogTo` peut être configuré pour enregistrer uniquement les messages d’une ou plusieurs catégories.</span><span class="sxs-lookup"><span data-stu-id="6178c-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="6178c-191">Par exemple, pour enregistrer uniquement les interactions entre les bases de données :</span><span class="sxs-lookup"><span data-stu-id="6178c-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="6178c-192">Notez que la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe fournit une API hiérarchique pour la recherche d’une catégorie et évite d’avoir à coder en dur des chaînes.</span><span class="sxs-lookup"><span data-stu-id="6178c-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="6178c-193">Étant donné que les catégories sont hiérarchiques, cet exemple qui utilise la `Database` catégorie inclut tous les messages des sous-catégories `Database.Connection` , `Database.Command` et `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="6178c-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="6178c-194">Filtres personnalisés</span><span class="sxs-lookup"><span data-stu-id="6178c-194">Custom filters</span></span>

<span data-ttu-id="6178c-195">`LogTo` permet d’utiliser un filtre personnalisé pour les cas où aucune des options de filtrage ci-dessus n’est suffisante.</span><span class="sxs-lookup"><span data-stu-id="6178c-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="6178c-196">Par exemple, pour enregistrer un message au niveau `Information` ou au-dessus, ainsi que des messages pour l’ouverture et la fermeture d’une connexion :</span><span class="sxs-lookup"><span data-stu-id="6178c-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="6178c-197">Le filtrage à l’aide de filtres personnalisés ou de l’une des autres options présentées ici est plus efficace que le filtrage dans le délégué LogTo.</span><span class="sxs-lookup"><span data-stu-id="6178c-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="6178c-198">En effet, si le filtre détermine que le message ne doit pas être consigné, le message du journal n’est même pas créé.</span><span class="sxs-lookup"><span data-stu-id="6178c-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="message-contents-and-formatting"></a><span data-ttu-id="6178c-199">Contenu et mise en forme du message</span><span class="sxs-lookup"><span data-stu-id="6178c-199">Message contents and formatting</span></span>

<span data-ttu-id="6178c-200">Le contenu par défaut de `LogTo` est mis en forme sur plusieurs lignes.</span><span class="sxs-lookup"><span data-stu-id="6178c-200">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="6178c-201">La première ligne contient les métadonnées du message :</span><span class="sxs-lookup"><span data-stu-id="6178c-201">The first line contains message metadata:</span></span>

* <span data-ttu-id="6178c-202"><xref:Microsoft.Extensions.Logging.LogLevel>En tant que préfixe à quatre caractères</span><span class="sxs-lookup"><span data-stu-id="6178c-202">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="6178c-203">Une estampille locale, mise en forme pour la culture actuelle</span><span class="sxs-lookup"><span data-stu-id="6178c-203">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="6178c-204"><xref:Microsoft.Extensions.Logging.EventId>Dans le formulaire qui peut être copié/collé pour récupérer le membre de <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> ou de l’une des autres `EventId` classes, plus la valeur d’ID brute</span><span class="sxs-lookup"><span data-stu-id="6178c-204">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="6178c-205">Catégorie d’événement, comme décrit ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6178c-205">The event category, as described above.</span></span>

<span data-ttu-id="6178c-206">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-206">For example:</span></span>

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

<span data-ttu-id="6178c-207">Ce contenu peut être personnalisé en passant des valeurs à partir de [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="6178c-207">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="6178c-208">, comme indiqué dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="6178c-208">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="6178c-209">Envisagez d’utiliser [Microsoft. extensions. Logging](/aspnet/core/fundamentals/logging) pour davantage de contrôle sur la mise en forme des journaux.</span><span class="sxs-lookup"><span data-stu-id="6178c-209">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="6178c-210">Utilisation de l’heure UTC</span><span class="sxs-lookup"><span data-stu-id="6178c-210">Using UTC time</span></span>

<span data-ttu-id="6178c-211">Par défaut, les timestamnps sont conçus pour la consommation locale pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="6178c-211">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="6178c-212">Utilisez `DbContextLoggerOptions.DefaultWithUtcTime` plutôt pour utiliser des horodatages UTC indépendantes de la culture, mais conservez tout le reste.</span><span class="sxs-lookup"><span data-stu-id="6178c-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="6178c-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-213">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="6178c-214">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="6178c-214">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="6178c-215">Journalisation sur une seule ligne</span><span class="sxs-lookup"><span data-stu-id="6178c-215">Single line logging</span></span>

<span data-ttu-id="6178c-216">Il est parfois utile d’avoir une seule ligne par message de journal.</span><span class="sxs-lookup"><span data-stu-id="6178c-216">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="6178c-217">Cette option peut être activée par `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="6178c-217">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="6178c-218">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-218">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="6178c-219">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="6178c-219">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="6178c-220">Autres options de contenu</span><span class="sxs-lookup"><span data-stu-id="6178c-220">Other content options</span></span>

<span data-ttu-id="6178c-221">Autres indicateurs dans [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="6178c-221">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="6178c-222">peut être utilisé pour réduire la quantité de métadonnées incluses dans le journal.</span><span class="sxs-lookup"><span data-stu-id="6178c-222">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="6178c-223">Cela peut être utile conjointement avec la journalisation sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="6178c-223">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="6178c-224">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-224">For example:</span></span>

<span data-ttu-id="6178c-225">Cet exemple entraîne la mise en forme suivante du journal :</span><span class="sxs-lookup"><span data-stu-id="6178c-225">This example results in the following log formatting:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="6178c-226">Déplacement à partir de EF6</span><span class="sxs-lookup"><span data-stu-id="6178c-226">Moving from EF6</span></span>

<span data-ttu-id="6178c-227">EF Core la journalisation simple diffère de <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6 de deux façons importantes :</span><span class="sxs-lookup"><span data-stu-id="6178c-227">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="6178c-228">Les messages de journal ne sont pas limités aux interactions de base de données uniquement</span><span class="sxs-lookup"><span data-stu-id="6178c-228">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="6178c-229">La journalisation doit être configurée au moment de l’initialisation du contexte</span><span class="sxs-lookup"><span data-stu-id="6178c-229">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="6178c-230">Pour la première différence, le filtrage décrit ci-dessus peut être utilisé pour limiter les messages à enregistrer.</span><span class="sxs-lookup"><span data-stu-id="6178c-230">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="6178c-231">La deuxième différence est une modification intentionnelle pour améliorer les performances en ne générant pas de messages de journal lorsqu’ils ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="6178c-231">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="6178c-232">Toutefois, il est toujours possible d’obtenir un comportement similaire à EF6 en créant une `Log` propriété sur votre `DbContext` , puis en l’utilisant uniquement lorsqu’elle a été définie.</span><span class="sxs-lookup"><span data-stu-id="6178c-232">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="6178c-233">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6178c-233">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
