---
title: Intercepteurs-EF Core
description: Interception pour les opérations de base de données et d’autres événements
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 22d860a083c5ece9be109be630c3ce01dd742bf2
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003408"
---
# <a name="interceptors"></a><span data-ttu-id="a63e2-103">Intercepteurs</span><span class="sxs-lookup"><span data-stu-id="a63e2-103">Interceptors</span></span>

<span data-ttu-id="a63e2-104">Les intercepteurs de Entity Framework Core (EF Core) permettent l’interception, la modification et/ou la suppression d’opérations de EF Core.</span><span class="sxs-lookup"><span data-stu-id="a63e2-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="a63e2-105">Cela comprend les opérations de base de données de bas niveau telles que l’exécution d’une commande, ainsi que les opérations de niveau supérieur, telles que les appels à SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="a63e2-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="a63e2-106">Les intercepteurs diffèrent de la journalisation et des diagnostics en ce qu’ils autorisent la modification ou la suppression de l’opération qui est interceptée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="a63e2-107">La journalisation [simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) sont de meilleures options pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a63e2-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="a63e2-108">Les intercepteurs sont inscrits par instance DbContext lorsque le contexte est configuré.</span><span class="sxs-lookup"><span data-stu-id="a63e2-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="a63e2-109">Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.</span><span class="sxs-lookup"><span data-stu-id="a63e2-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="a63e2-110">Inscription des intercepteurs</span><span class="sxs-lookup"><span data-stu-id="a63e2-110">Registering interceptors</span></span>

<span data-ttu-id="a63e2-111">Les intercepteurs sont inscrits à l’aide <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [de lors de la configuration d’une instance DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a63e2-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="a63e2-112">Cela se fait généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a63e2-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a63e2-113">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a63e2-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="a63e2-114">Vous pouvez également `AddInterceptors` appeler dans le cadre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ou lors de la création d’une <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance à passer au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="a63e2-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="a63e2-115">OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="a63e2-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="a63e2-116">C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.</span><span class="sxs-lookup"><span data-stu-id="a63e2-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="a63e2-117">Les intercepteurs sont souvent sans État, ce qui signifie qu’une seule instance d’intercepteur peut être utilisée pour toutes les instances de DbContext.</span><span class="sxs-lookup"><span data-stu-id="a63e2-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="a63e2-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a63e2-118">For example:</span></span>

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

<span data-ttu-id="a63e2-119">Chaque instance d’intercepteur doit implémenter une ou plusieurs interfaces dérivées de <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> .</span><span class="sxs-lookup"><span data-stu-id="a63e2-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="a63e2-120">Chaque instance doit être inscrite une seule fois, même si elle implémente plusieurs interfaces d’interception. EF Core acheminera les événements pour chaque interface comme il convient.</span><span class="sxs-lookup"><span data-stu-id="a63e2-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="a63e2-121">Interception de base de données</span><span class="sxs-lookup"><span data-stu-id="a63e2-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="a63e2-122">L’interception de base de données a été introduite dans EF Core 3,0 et n’est disponible que pour les fournisseurs de bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="a63e2-122">Database interception was introduced in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="a63e2-123">La prise en charge des point de enregistrement a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a63e2-123">Savepoint support was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="a63e2-124">L’interception de base de données de bas niveau est divisée en trois interfaces, présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="a63e2-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="a63e2-125">Intercepteur</span><span class="sxs-lookup"><span data-stu-id="a63e2-125">Interceptor</span></span>                                                            | <span data-ttu-id="a63e2-126">Opérations de base de données interceptées</span><span class="sxs-lookup"><span data-stu-id="a63e2-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="a63e2-127">Créer des commandes</span><span class="sxs-lookup"><span data-stu-id="a63e2-127">Creating commands</span></span></br><span data-ttu-id="a63e2-128">Exécution de commandes</span><span class="sxs-lookup"><span data-stu-id="a63e2-128">Executing commands</span></span></br><span data-ttu-id="a63e2-129">Échecs de commande</span><span class="sxs-lookup"><span data-stu-id="a63e2-129">Command failures</span></span></br><span data-ttu-id="a63e2-130">Suppression du DbDataReader de la commande</span><span class="sxs-lookup"><span data-stu-id="a63e2-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="a63e2-131">Ouverture et fermeture des connexions</span><span class="sxs-lookup"><span data-stu-id="a63e2-131">Opening and closing connections</span></span></br><span data-ttu-id="a63e2-132">Échecs de connexion</span><span class="sxs-lookup"><span data-stu-id="a63e2-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="a63e2-133">Création de transactions</span><span class="sxs-lookup"><span data-stu-id="a63e2-133">Creating transactions</span></span></br><span data-ttu-id="a63e2-134">Utilisation de transactions existantes</span><span class="sxs-lookup"><span data-stu-id="a63e2-134">Using existing transactions</span></span></br><span data-ttu-id="a63e2-135">Validation des transactions</span><span class="sxs-lookup"><span data-stu-id="a63e2-135">Committing transactions</span></span></br><span data-ttu-id="a63e2-136">Restauration des transactions</span><span class="sxs-lookup"><span data-stu-id="a63e2-136">Rolling back transactions</span></span></br><span data-ttu-id="a63e2-137">Création et utilisation de points d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="a63e2-137">Creating and using savepoints</span></span></br><span data-ttu-id="a63e2-138">Échecs de transaction</span><span class="sxs-lookup"><span data-stu-id="a63e2-138">Transaction failures</span></span>

<span data-ttu-id="a63e2-139">Les classes de base <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> et <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contiennent des implémentations de non-op pour chaque méthode dans l’interface correspondante.</span><span class="sxs-lookup"><span data-stu-id="a63e2-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="a63e2-140">Utilisez les classes de base pour éviter d’avoir à implémenter des méthodes d’interception inutilisées.</span><span class="sxs-lookup"><span data-stu-id="a63e2-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="a63e2-141">Les méthodes sur chaque type d’intercepteur sont en paire, avec la première appelée avant le démarrage de l’opération de base de données, et la seconde après la fin de l’opération.</span><span class="sxs-lookup"><span data-stu-id="a63e2-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="a63e2-142">Par exemple,</span><span class="sxs-lookup"><span data-stu-id="a63e2-142">For example.</span></span> <span data-ttu-id="a63e2-143">Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> est appelé avant l’exécution d’une requête, et <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> est appelé après que la requête a été envoyée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="a63e2-144">Chaque paire de méthodes a des variantes synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="a63e2-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="a63e2-145">Cela permet d’effectuer des e/s asynchrones, telles que la demande d’un jeton d’accès, dans le cadre de l’interception d’une opération de base de données asynchrone.</span><span class="sxs-lookup"><span data-stu-id="a63e2-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="a63e2-146">Exemple : interception de commande pour ajouter des indicateurs de requête</span><span class="sxs-lookup"><span data-stu-id="a63e2-146">Example: Command interception to add query hints</span></span>

> [!TIP]  
> <span data-ttu-id="a63e2-147">Vous pouvez [Télécharger l’exemple d’intercepteur de commande](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="a63e2-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="a63e2-148">Un <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> peut être utilisé pour modifier SQL avant d’être envoyé à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="a63e2-149">Cet exemple montre comment modifier le SQL pour inclure un indicateur de requête.</span><span class="sxs-lookup"><span data-stu-id="a63e2-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="a63e2-150">Souvent, la partie la plus délicate de l’interception consiste à déterminer quand la commande correspond à la requête qui doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="a63e2-151">L’analyse de SQL est une option, mais elle a tendance à être fragile.</span><span class="sxs-lookup"><span data-stu-id="a63e2-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="a63e2-152">Une autre option consiste à utiliser des [balises de requête EF Core](xref:core/querying/tags) pour baliser chaque requête qui doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="a63e2-153">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a63e2-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="a63e2-154">Cette balise peut ensuite être détectée dans l’intercepteur, car elle sera toujours incluse en tant que commentaire dans la première ligne du texte de la commande.</span><span class="sxs-lookup"><span data-stu-id="a63e2-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="a63e2-155">Lors de la détection de la balise, la requête SQL est modifiée pour ajouter l’indicateur approprié :</span><span class="sxs-lookup"><span data-stu-id="a63e2-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

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

<span data-ttu-id="a63e2-156">Remarques :</span><span class="sxs-lookup"><span data-stu-id="a63e2-156">Notice:</span></span>

* <span data-ttu-id="a63e2-157">L’intercepteur hérite de <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> pour éviter d’avoir à implémenter chaque méthode dans l’interface d’intercepteur.</span><span class="sxs-lookup"><span data-stu-id="a63e2-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="a63e2-158">L’intercepteur implémente des méthodes synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="a63e2-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="a63e2-159">Cela permet de s’assurer que le même indicateur de requête est appliqué aux requêtes Sync et Async.</span><span class="sxs-lookup"><span data-stu-id="a63e2-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="a63e2-160">L’intercepteur implémente les `Executing` méthodes qui sont appelées par EF Core avec le SQL généré _avant_ qu’il ne soit envoyé à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="a63e2-161">Comparez ceci avec les `Executed` méthodes, qui sont appelées après le retour de l’appel de base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="a63e2-162">L’exécution du code dans cet exemple génère ce qui suit lorsqu’une requête est marquée :</span><span class="sxs-lookup"><span data-stu-id="a63e2-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="a63e2-163">En revanche, lorsqu’une requête n’est pas marquée, elle est envoyée à la base de données comme étant non modifiée :</span><span class="sxs-lookup"><span data-stu-id="a63e2-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="a63e2-164">Exemple : interception de connexion pour l’authentification SQL Azure à l’aide de ADD</span><span class="sxs-lookup"><span data-stu-id="a63e2-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]  
> <span data-ttu-id="a63e2-165">Vous pouvez [Télécharger l’exemple d’intercepteur de connexion](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="a63e2-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="a63e2-166">Un <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> peut être utilisé pour manipuler le <xref:System.Data.Common.DbConnection> avant qu’il ne soit utilisé pour se connecter à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="a63e2-167">Cela peut être utilisé pour obtenir un jeton d’accès Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="a63e2-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="a63e2-168">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a63e2-168">For example:</span></span>

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
> <span data-ttu-id="a63e2-169">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) prend désormais en charge l’authentification AAD via la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a63e2-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="a63e2-170">Consultez la rubrique <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="a63e2-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="a63e2-171">Notez que l’intercepteur lève une exception si un appel de synchronisation est effectué pour ouvrir la connexion.</span><span class="sxs-lookup"><span data-stu-id="a63e2-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="a63e2-172">Cela est dû au fait qu’il n’existe aucune méthode non asynchrone pour obtenir le jeton d’accès et qu’il n’existe [aucun moyen universel et simple d’appeler une méthode Async à partir d’un contexte non asynchrone sans risquer d’interblocage](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span><span class="sxs-lookup"><span data-stu-id="a63e2-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="a63e2-173">dans certains cas, le jeton d’accès ne peut pas être automatiquement mis en cache par le fournisseur de jetons Azure.</span><span class="sxs-lookup"><span data-stu-id="a63e2-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="a63e2-174">Selon le type de jeton demandé, vous devrez peut-être implémenter votre propre mise en cache ici.</span><span class="sxs-lookup"><span data-stu-id="a63e2-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="a63e2-175">Exemple : interception de commande avancée pour la mise en cache</span><span class="sxs-lookup"><span data-stu-id="a63e2-175">Example: Advanced command interception for caching</span></span>

> [!TIP]  
> <span data-ttu-id="a63e2-176">Vous pouvez [Télécharger l’exemple d’intercepteur de commandes avancé](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="a63e2-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="a63e2-177">Les intercepteurs de EF Core peuvent :</span><span class="sxs-lookup"><span data-stu-id="a63e2-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="a63e2-178">Indiquer à EF Core de supprimer l’exécution de l’opération en cours d’interception</span><span class="sxs-lookup"><span data-stu-id="a63e2-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="a63e2-179">Modifier le résultat de l’opération signalée à EF Core</span><span class="sxs-lookup"><span data-stu-id="a63e2-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="a63e2-180">Cet exemple montre un intercepteur qui utilise ces fonctionnalités pour se comporter comme un cache de second niveau primitif.</span><span class="sxs-lookup"><span data-stu-id="a63e2-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="a63e2-181">Les résultats de requête mis en cache sont retournés pour une requête spécifique, évitant ainsi l’aller-retour d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="a63e2-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="a63e2-182">Veillez à modifier le comportement par défaut EF Core de cette manière.</span><span class="sxs-lookup"><span data-stu-id="a63e2-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="a63e2-183">EF Core peut se comporter de manière inattendue s’il obtient un résultat anormal qu’il ne peut pas traiter correctement.</span><span class="sxs-lookup"><span data-stu-id="a63e2-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="a63e2-184">En outre, cet exemple illustre les concepts de l’intercepteur. Il n’est pas conçu comme un modèle pour une implémentation de cache de second niveau robuste.</span><span class="sxs-lookup"><span data-stu-id="a63e2-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="a63e2-185">Dans cet exemple, l’application exécute fréquemment une requête pour obtenir le « message quotidien » le plus récent :</span><span class="sxs-lookup"><span data-stu-id="a63e2-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="a63e2-186">Cette requête est [marquée](xref:core/querying/tags) pour pouvoir être facilement détectée dans l’intercepteur.</span><span class="sxs-lookup"><span data-stu-id="a63e2-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="a63e2-187">L’idée est de n’interroger la base de données que pour obtenir un nouveau message une fois par jour.</span><span class="sxs-lookup"><span data-stu-id="a63e2-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="a63e2-188">À d’autres moments, l’application utilisera un résultat mis en cache.</span><span class="sxs-lookup"><span data-stu-id="a63e2-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="a63e2-189">(L’exemple utilise le délai de 10 secondes dans l’échantillon pour simuler un nouveau jour.)</span><span class="sxs-lookup"><span data-stu-id="a63e2-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="a63e2-190">État de l’intercepteur</span><span class="sxs-lookup"><span data-stu-id="a63e2-190">Interceptor state</span></span>

<span data-ttu-id="a63e2-191">Cet intercepteur est avec état : il stocke l’ID et le texte du message le plus récent quotidiennement interrogé, ainsi que l’heure à laquelle cette requête a été exécutée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="a63e2-192">En raison de cet État, nous avons également besoin d’un [verrou](/dotnet/csharp/language-reference/keywords/lock-statement) dans la mesure où la mise en cache nécessite que le même intercepteur soit utilisé par plusieurs instances de contexte.</span><span class="sxs-lookup"><span data-stu-id="a63e2-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="a63e2-193">Avant l'exécution</span><span class="sxs-lookup"><span data-stu-id="a63e2-193">Before execution</span></span>

<span data-ttu-id="a63e2-194">Dans la `Executing` méthode (par exemple, avant d’effectuer un appel de base de données), l’intercepteur détecte la requête avec balises, puis vérifie s’il existe un résultat mis en cache.</span><span class="sxs-lookup"><span data-stu-id="a63e2-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="a63e2-195">Si un résultat de ce type est trouvé, la requête est supprimée et les résultats mis en cache sont utilisés à la place.</span><span class="sxs-lookup"><span data-stu-id="a63e2-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

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

<span data-ttu-id="a63e2-196">Notez que le code appelle <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> et passe un remplacement <xref:System.Data.Common.DbDataReader> contenant les données mises en cache.</span><span class="sxs-lookup"><span data-stu-id="a63e2-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="a63e2-197">Ce InterceptionResult est ensuite retourné, provoquant la suppression de l’exécution de la requête.</span><span class="sxs-lookup"><span data-stu-id="a63e2-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="a63e2-198">Le lecteur de remplacement est utilisé à la place par EF Core comme résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="a63e2-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="a63e2-199">Cet intercepteur manipule également le texte de la commande.</span><span class="sxs-lookup"><span data-stu-id="a63e2-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="a63e2-200">Cette manipulation n’est pas obligatoire, mais elle améliore la clarté des messages de journal.</span><span class="sxs-lookup"><span data-stu-id="a63e2-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="a63e2-201">Le texte de la commande n’a pas besoin d’être un SQL valide, car la requête n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="a63e2-202">Après l’exécution</span><span class="sxs-lookup"><span data-stu-id="a63e2-202">After execution</span></span>

<span data-ttu-id="a63e2-203">Si aucun message mis en cache n’est disponible, ou s’il a expiré, le code ci-dessus ne supprime pas le résultat.</span><span class="sxs-lookup"><span data-stu-id="a63e2-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="a63e2-204">EF Core exécute donc la requête normalement.</span><span class="sxs-lookup"><span data-stu-id="a63e2-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="a63e2-205">Elle retournera ensuite à la méthode de l’intercepteur `Executed` après l’exécution.</span><span class="sxs-lookup"><span data-stu-id="a63e2-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="a63e2-206">À ce stade, si le résultat n’est pas déjà un lecteur mis en cache, l’ID et la chaîne du nouveau message sont exacts du lecteur réel et mis en cache pour la prochaine utilisation de cette requête.</span><span class="sxs-lookup"><span data-stu-id="a63e2-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="a63e2-207">Démonstration</span><span class="sxs-lookup"><span data-stu-id="a63e2-207">Demonstration</span></span>

<span data-ttu-id="a63e2-208">L' [exemple d’intercepteur de mise en cache](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contient une application console simple qui interroge les messages quotidiens pour tester la mise en cache :</span><span class="sxs-lookup"><span data-stu-id="a63e2-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

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

<span data-ttu-id="a63e2-209">Il s'ensuit la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a63e2-209">This results in the following output:</span></span>

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

<span data-ttu-id="a63e2-210">Notez que dans la sortie du journal, l’application continue à utiliser le message mis en cache jusqu’à l’expiration du délai d’attente. à ce moment-là, la base de données est à nouveau interrogée pour tout nouveau message.</span><span class="sxs-lookup"><span data-stu-id="a63e2-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="a63e2-211">Interception d’SaveChanges</span><span class="sxs-lookup"><span data-stu-id="a63e2-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="a63e2-212">L’interception d’SaveChanges a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a63e2-212">SaveChanges interception was introduced in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="a63e2-213">Vous pouvez [Télécharger l’exemple d’intercepteur SaveChanges](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="a63e2-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="a63e2-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>les points d’interception et sont définis par le`ISaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="a63e2-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the `ISaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-215">interface.</span><span class="sxs-lookup"><span data-stu-id="a63e2-215">interface.</span></span> <span data-ttu-id="a63e2-216">Comme pour les autres intercepteurs, le `SaveChangesInterceptor`</span><span class="sxs-lookup"><span data-stu-id="a63e2-216">As for other interceptors, the `SaveChangesInterceptor`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-217">la classe de base avec des méthodes sans op est fournie à titre de commodité.</span><span class="sxs-lookup"><span data-stu-id="a63e2-217">base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="a63e2-218">Les intercepteurs sont puissants.</span><span class="sxs-lookup"><span data-stu-id="a63e2-218">Interceptors are powerful.</span></span> <span data-ttu-id="a63e2-219">Toutefois, dans de nombreux cas, il peut être plus facile de remplacer la méthode SaveChanges ou [d’utiliser les événements .net pour SaveChanges](xref:core/logging-events-diagnostics/events) exposés sur DbContext.</span><span class="sxs-lookup"><span data-stu-id="a63e2-219">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="a63e2-220">Exemple : interception d’SaveChanges pour l’audit</span><span class="sxs-lookup"><span data-stu-id="a63e2-220">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="a63e2-221">SaveChanges peut être intercepté pour créer un enregistrement d’audit indépendant des modifications apportées.</span><span class="sxs-lookup"><span data-stu-id="a63e2-221">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="a63e2-222">Cela n’est pas destiné à être une solution d’audit fiable.</span><span class="sxs-lookup"><span data-stu-id="a63e2-222">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="a63e2-223">Il s’agit plutôt d’un exemple simpliste utilisé pour illustrer les fonctionnalités d’interception.</span><span class="sxs-lookup"><span data-stu-id="a63e2-223">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="a63e2-224">Contexte de l’application</span><span class="sxs-lookup"><span data-stu-id="a63e2-224">The application context</span></span>

<span data-ttu-id="a63e2-225">L' [exemple d’audit](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) utilise un DbContext simple avec les blogs et les publications.</span><span class="sxs-lookup"><span data-stu-id="a63e2-225">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

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

<span data-ttu-id="a63e2-226">Notez qu’une nouvelle instance de l’intercepteur est inscrite pour chaque instance de DbContext.</span><span class="sxs-lookup"><span data-stu-id="a63e2-226">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="a63e2-227">Cela est dû au fait que l’intercepteur d’audit contient l’État lié à l’instance de contexte actuelle.</span><span class="sxs-lookup"><span data-stu-id="a63e2-227">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="a63e2-228">Contexte d’audit</span><span class="sxs-lookup"><span data-stu-id="a63e2-228">The audit context</span></span>

<span data-ttu-id="a63e2-229">L’exemple contient également un deuxième DbContext et un modèle utilisés pour la base de données d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-229">The sample also contains a second DbContext and model used for the auditing database.</span></span>

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

#### <a name="the-interceptor"></a><span data-ttu-id="a63e2-230">L’intercepteur</span><span class="sxs-lookup"><span data-stu-id="a63e2-230">The interceptor</span></span>

<span data-ttu-id="a63e2-231">L’idée générale de l’audit avec l’intercepteur est la suivante :</span><span class="sxs-lookup"><span data-stu-id="a63e2-231">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="a63e2-232">Un message d’audit est créé au début de SaveChanges et écrit dans la base de données d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-232">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="a63e2-233">SaveChanges est autorisé à continuer</span><span class="sxs-lookup"><span data-stu-id="a63e2-233">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="a63e2-234">Si SaveChanges réussit, le message d’audit est mis à jour pour indiquer la réussite</span><span class="sxs-lookup"><span data-stu-id="a63e2-234">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="a63e2-235">Si SaveChanges échoue, le message d’audit est mis à jour pour indiquer l’échec</span><span class="sxs-lookup"><span data-stu-id="a63e2-235">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="a63e2-236">La première étape est gérée avant l’envoi des modifications à la base de données à l’aide des remplacements de `ISaveChangesInterceptor.SavingChanges`</span><span class="sxs-lookup"><span data-stu-id="a63e2-236">The first stage is handled before any changes are sent to the database using overrides of `ISaveChangesInterceptor.SavingChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-237"> et `ISaveChangesInterceptor.SavingChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="a63e2-237">and `ISaveChangesInterceptor.SavingChangesAsync`</span></span><!-- Issue #2748 --><span data-ttu-id="a63e2-238">.</span><span class="sxs-lookup"><span data-stu-id="a63e2-238">.</span></span>

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

<span data-ttu-id="a63e2-239">La substitution des méthodes synchrones et asynchrones garantit que l’audit aura lieu indépendamment du fait que SaveChanges ou SaveChangesAsync sont appelés.</span><span class="sxs-lookup"><span data-stu-id="a63e2-239">Overriding both sync and async methods ensures that auditing will happen regardless of whether SaveChanges or SaveChangesAsync are called.</span></span> <span data-ttu-id="a63e2-240">Notez également que la surcharge Async est elle-même en mesure d’effectuer des e/s asynchrones non bloquantes dans la base de données d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-240">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="a63e2-241">Vous pouvez effectuer une exception à partir de la méthode Sync SavingChanges pour vous assurer que toutes les e/s de base de données sont asynchrones.</span><span class="sxs-lookup"><span data-stu-id="a63e2-241">You may wish to throw from the sync SavingChanges method to ensure that all database I/O is async.</span></span> <span data-ttu-id="a63e2-242">Cela nécessite ensuite que l’application appelle toujours SaveChangesAsync et jamais SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="a63e2-242">This then requires that the application always calls SaveChangesAsync and never SaveChanges.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="a63e2-243">Message d’audit</span><span class="sxs-lookup"><span data-stu-id="a63e2-243">The audit message</span></span>

<span data-ttu-id="a63e2-244">Chaque méthode d’intercepteur a un `eventData` paramètre qui fournit des informations contextuelles sur l’événement qui est intercepté.</span><span class="sxs-lookup"><span data-stu-id="a63e2-244">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="a63e2-245">Dans ce cas, le DbContext d’application actuel est inclus dans les données d’événement, qui est ensuite utilisé pour créer un message d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-245">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

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

<span data-ttu-id="a63e2-246">Le résultat est une `SaveChangesAudit` entité avec une collection d' `EntityAudit` entités, une pour chaque insertion, mise à jour ou suppression.</span><span class="sxs-lookup"><span data-stu-id="a63e2-246">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="a63e2-247">L’intercepteur insère ensuite ces entités dans la base de données d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-247">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="a63e2-248">ToString est substitué dans chaque classe de données d’événement EF Core pour générer le message de journal équivalent pour l’événement.</span><span class="sxs-lookup"><span data-stu-id="a63e2-248">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="a63e2-249">Par exemple, l’appel de `ContextInitializedEventData.ToString` génère « Entity Framework Core 5.0.0 a initialisé «BlogsContext » à l’aide du fournisseur « Microsoft. EntityFrameworkCore. sqlite » avec options : None».</span><span class="sxs-lookup"><span data-stu-id="a63e2-249">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="a63e2-250">Détection du succès</span><span class="sxs-lookup"><span data-stu-id="a63e2-250">Detecting success</span></span>

<span data-ttu-id="a63e2-251">L’entité d’audit est stockée sur l’intercepteur afin de pouvoir y accéder à nouveau une fois que SaveChanges réussit ou échoue.</span><span class="sxs-lookup"><span data-stu-id="a63e2-251">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="a63e2-252">En cas de réussite, `ISaveChangesInterceptor.SavedChanges`</span><span class="sxs-lookup"><span data-stu-id="a63e2-252">For success, `ISaveChangesInterceptor.SavedChanges`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-253">ou `ISaveChangesInterceptor.SavedChangesAsync`</span><span class="sxs-lookup"><span data-stu-id="a63e2-253">or `ISaveChangesInterceptor.SavedChangesAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-254">La méthode  est appelée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-254">is called.</span></span>

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

<span data-ttu-id="a63e2-255">L’entité d’audit est attachée au contexte d’audit, car elle existe déjà dans la base de données et doit être mise à jour.</span><span class="sxs-lookup"><span data-stu-id="a63e2-255">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="a63e2-256">Nous définissons ensuite `Succeeded` et `EndTime` , qui marque ces propriétés comme modifiées, si SaveChanges enverra une mise à jour à la base de données d’audit.</span><span class="sxs-lookup"><span data-stu-id="a63e2-256">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="a63e2-257">Détection de défaillance</span><span class="sxs-lookup"><span data-stu-id="a63e2-257">Detecting failure</span></span>

<span data-ttu-id="a63e2-258">L’échec est géré à peu près de la même façon que le succès, mais dans la `ISaveChangesInterceptor.SaveChangesFailed`</span><span class="sxs-lookup"><span data-stu-id="a63e2-258">Failure is handled in much the same way as success, but in the `ISaveChangesInterceptor.SaveChangesFailed`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-259">ou `ISaveChangesInterceptor.SaveChangesFailedAsync`</span><span class="sxs-lookup"><span data-stu-id="a63e2-259">or `ISaveChangesInterceptor.SaveChangesFailedAsync`</span></span> <!-- Issue #2748 --> <span data-ttu-id="a63e2-260">.</span><span class="sxs-lookup"><span data-stu-id="a63e2-260">method.</span></span> <span data-ttu-id="a63e2-261">Les données d’événement contiennent l’exception qui a été levée.</span><span class="sxs-lookup"><span data-stu-id="a63e2-261">The event data contains the exception that was thrown.</span></span>

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

#### <a name="demonstration"></a><span data-ttu-id="a63e2-262">Démonstration</span><span class="sxs-lookup"><span data-stu-id="a63e2-262">Demonstration</span></span>

<span data-ttu-id="a63e2-263">L' [exemple d’audit](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contient une application console simple qui apporte des modifications à la base de données de création de blogs, puis affiche l’audit qui a été créé.</span><span class="sxs-lookup"><span data-stu-id="a63e2-263">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

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

<span data-ttu-id="a63e2-264">Le résultat affiche le contenu de la base de données d’audit :</span><span class="sxs-lookup"><span data-stu-id="a63e2-264">The result shows the contents of the auditing database:</span></span>

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