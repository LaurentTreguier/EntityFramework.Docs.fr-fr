---
title: Journalisation et interception des opérations de base de données-EF6
description: Journalisation et interception des opérations de base de données dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: bb5c3392b4f2e1f291d7ac373d07724f56d0eb30
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616199"
---
# <a name="logging-and-intercepting-database-operations"></a>Journalisation et interception des opérations de base de données
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

À partir de Entity Framework 6, chaque fois que Entity Framework envoie une commande à la base de données, cette commande peut être interceptée par le code de l’application. C’est le plus souvent utilisé pour la journalisation SQL, mais elle peut également être utilisée pour modifier ou abandonner la commande.  

En particulier, EF comprend les éléments suivants :  

- Une propriété de journal pour le contexte similaire à DataContext. log dans LINQ to SQL  
- Mécanisme de personnalisation du contenu et de la mise en forme de la sortie envoyée au Journal  
- Blocs de construction de bas niveau pour l’interception donnant plus de contrôle/flexibilité  

## <a name="context-log-property"></a>Propriété du journal de contexte  

La propriété DbContext. Database. log peut avoir pour valeur un délégué pour toute méthode qui prend une chaîne. Le plus souvent, il est utilisé avec n’importe quel TextWriter en le définissant sur la méthode « Write » de ce TextWriter. Toutes les SQL générées par le contexte actuel seront enregistrées dans ce writer. Par exemple, le code suivant consignera SQL sur la console :  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

Notez ce contexte. Database. log est défini sur console. Write. C’est tout ce qui est nécessaire pour enregistrer SQL sur la console.  

Nous allons ajouter un code simple de requête/insertion/mise à jour pour pouvoir voir une sortie :  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

La sortie suivante est générée :  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

(Notez qu’il s’agit de la sortie en supposant que toutes les initialisations de base de données ont déjà eu lieu. Si l’initialisation de la base de données ne s’est pas encore produite, il y aurait beaucoup plus de sortie qui indique que toutes les migrations de travail effectuées dans le cadre de la recherche ou de la création d’une nouvelle base de données.)  

## <a name="what-gets-logged"></a>Qu’est-ce qui est enregistré ?  

Lorsque la propriété log est définie, tous les éléments suivants sont enregistrés :  

- SQL pour tous les différents types de commandes. Par exemple :  
    - Requêtes, y compris les requêtes LINQ normales, les requêtes eSQL et les requêtes brutes à partir de méthodes telles que SqlQuery  
    - Insertions, mises à jour et suppressions générées dans le cadre d’SaveChanges  
    - Requêtes de chargement des relations, telles que celles générées par le chargement différé  
- Paramètres  
- Indique si la commande est exécutée de façon asynchrone  
- Horodateur indiquant le début de l’exécution de la commande  
- Si la commande a été exécutée avec succès, a échoué en levant une exception ou, pour Async, a été annulée  
- Indication de la valeur de résultat  
- Durée approximative nécessaire à l’exécution de la commande. Notez qu’il s’agit de l’heure à partir de l’envoi de la commande pour récupérer l’objet de résultat. Elle n’inclut pas le temps nécessaire pour lire les résultats.  

En examinant l’exemple de sortie ci-dessus, chacune des quatre commandes journalisées est la suivante :  

- Requête résultant de l’appel au contexte. Blogs.  
    - Notez que la méthode ToString pour obtenir le SQL n’aurait pas fonctionné pour cette requête puisque « First » ne fournit pas de IQueryable sur lequel ToString pourrait être appelé  
- Requête résultant du chargement différé du blog. Publié  
    - Notez les détails des paramètres pour la valeur de clé pour laquelle le chargement différé se produit  
    - Seules les propriétés du paramètre définies à des valeurs non définies par défaut sont journalisées. Par exemple, la propriété Size s’affiche uniquement si elle est différente de zéro.  
- Deux commandes résultant de SaveChangesAsync ; une pour la mise à jour pour modifier un titre de publication, l’autre pour une insertion pour ajouter une nouvelle publication  
    - Notez les détails des paramètres pour les propriétés FK et title  
    - Notez que ces commandes sont exécutées de façon asynchrone  

## <a name="logging-to-different-places"></a>Journalisation dans différents emplacements  

Comme indiqué ci-dessus, la journalisation dans la console est très facile. Il est également facile de se connecter à la mémoire, au fichier, etc. en utilisant différents genres de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).  

Si vous êtes familiarisé avec LINQ to SQL vous pouvez remarquer que dans LINQ to SQL la propriété log est définie sur l’objet TextWriter réel (par exemple, console. out) alors que dans EF, la propriété log est définie sur une méthode qui accepte une chaîne (par exemple, console. Write ou console. out. Write). Cela consiste à découpler EF de TextWriter en acceptant tout délégué pouvant agir en tant que récepteur pour les chaînes. Par exemple, imaginez que vous disposez déjà d’une infrastructure de journalisation et qu’elle définit une méthode de journalisation comme :  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

Cela peut être raccordé à la propriété du journal EF comme suit :  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>Journalisation des résultats  

L’enregistreur d’événements par défaut enregistre le texte de la commande (SQL), les paramètres et la ligne « en cours d’exécution » avec un horodateur avant que la commande ne soit envoyée à la base de données. Une ligne « terminée » contenant le temps écoulé est journalisée après l’exécution de la commande.  

Notez que, pour les commandes Async, la ligne « terminé » n’est journalisée que lorsque la tâche asynchrone se termine, échoue ou est annulée.  

La ligne « terminé » contient des informations différentes selon le type de commande et si l’exécution a réussi ou non.  

### <a name="successful-execution"></a>Exécution réussie  

Pour les commandes qui se terminent correctement, la sortie est « terminée en x ms avec le résultat : », suivie d’une indication du résultat. Pour les commandes qui retournent un lecteur de données, l’indication du résultat est le type de [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) retourné. Pour les commandes qui retournent une valeur entière, comme la commande de mise à jour affichée au-dessus du résultat affiché, est cet entier.  

### <a name="failed-execution"></a>Échec de l’exécution  

Pour les commandes qui échouent en levant une exception, la sortie contient le message de l’exception. Par exemple, l’utilisation de SqlQuery pour effectuer des requêtes sur une table qui existe entraîne une sortie de journal similaire à ce qui suit :  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>Exécution annulée  

Pour les commandes Async où la tâche est annulée, le résultat peut être un échec avec une exception, car c’est ce que fait souvent le fournisseur ADO.NET sous-jacent lorsqu’une tentative d’annulation est effectuée. Si cela ne se produit pas et que la tâche est annulée correctement, la sortie se présente comme suit :  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>Modification du contenu et de la mise en forme du journal  

En coulisses, la propriété Database. log utilise un objet DatabaseLogFormatter. Cet objet lie efficacement une implémentation de IDbCommandInterceptor (voir ci-dessous) à un délégué qui accepte des chaînes et un DbContext. Cela signifie que les méthodes sur DatabaseLogFormatter sont appelées avant et après l’exécution de commandes par EF. Ces méthodes DatabaseLogFormatter rassemblent et mettent en forme la sortie de journal et l’envoient au délégué.  

### <a name="customizing-databaselogformatter"></a>Personnalisation de DatabaseLogFormatter  

La modification de ce qui est enregistré et de sa mise en forme peut être obtenue en créant une nouvelle classe qui dérive de DatabaseLogFormatter et substitue les méthodes selon le cas. Les méthodes les plus courantes de remplacement sont les suivantes :  

- LogCommand : remplacez cette valeur pour modifier la façon dont les commandes sont journalisées avant leur exécution. Par défaut, LogCommand appelle LogParameter pour chaque paramètre ; vous pouvez choisir d’effectuer la même opération dans votre remplacement ou gérer les paramètres différemment.  
- LogResult : remplacez cette valeur pour modifier le mode de journalisation du résultat de l’exécution d’une commande.  
- LogParameter : remplacez cette valeur pour modifier la mise en forme et le contenu de la journalisation des paramètres.  

Par exemple, supposons que nous voulions enregistrer une seule ligne avant que chaque commande ne soit envoyée à la base de données. Pour ce faire, vous pouvez utiliser deux remplacements :  

- Remplacer LogCommand pour mettre en forme et écrire la ligne unique de SQL  
- Remplacez LogResult pour ne rien faire.  

Le code se présenterait comme ceci :

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

Pour consigner la sortie, appelez simplement la méthode Write qui enverra la sortie au délégué d’écriture configuré.  

(Notez que ce code effectue une suppression simpliste des sauts de ligne comme un exemple. Elle ne fonctionnera probablement pas correctement pour l’affichage de SQL complexe.)  

### <a name="setting-the-databaselogformatter"></a>Définition de DatabaseLogFormatter  

Une fois qu’une nouvelle classe DatabaseLogFormatter a été créée, elle doit être inscrite auprès d’EF. Cette opération s’effectue à l’aide de la configuration basée sur le code. En résumé, cela implique la création d’une nouvelle classe qui dérive de DbConfiguration dans le même assembly que votre classe DbContext, puis l’appel de SetDatabaseLogFormatter dans le constructeur de cette nouvelle classe. Par exemple :  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>Utilisation du nouveau DatabaseLogFormatter  

Ce nouveau DatabaseLogFormatter sera désormais utilisé à chaque fois que Database. log est défini. Par conséquent, l’exécution du code de la partie 1 génère désormais la sortie suivante :  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>Blocs de construction d’interception  

Jusqu’à présent, nous avons vu comment utiliser DbContext. Database. log pour journaliser le SQL généré par EF. Mais ce code est en fait une façade relativement légère sur certains blocs de construction de bas niveau pour une interception plus générale.  

### <a name="interception-interfaces"></a>Interfaces d’interception  

Le code d’interception est construit autour du concept d’interfaces d’interception. Ces interfaces héritent de IDbInterceptor et définissent les méthodes appelées quand EF effectue une action. L’objectif est d’avoir une interface par type d’objet qui est intercepté. Par exemple, l’interface IDbCommandInterceptor définit des méthodes qui sont appelées avant que EF effectue un appel à ExecuteNonQuery, ExecuteScalar, ExecuteReader et les méthodes associées. De même, l’interface définit les méthodes qui sont appelées lorsque chacune de ces opérations se termine. La classe DatabaseLogFormatter que nous avons examinée ci-dessus implémente cette interface pour enregistrer des commandes.  

### <a name="the-interception-context"></a>Contexte d’interception  

En examinant les méthodes définies sur l’une des interfaces d’intercepteur, il est évident que chaque appel reçoit un objet de type DbInterceptionContext ou un type dérivé de celui-ci, tel que DbCommandInterceptionContext \<\> . Cet objet contient des informations contextuelles sur l’action effectuée par EF. Par exemple, si l’action est effectuée pour le compte d’un DbContext, le DbContext est inclus dans le DbInterceptionContext. De même, pour les commandes qui sont exécutées de façon asynchrone, l’indicateur IsAsync est défini sur DbCommandInterceptionContext.  

### <a name="result-handling"></a>Traitement des résultats  

La \<\> classe DbCommandInterceptionContext contient une propriété appelée result, OriginalResult, exception et OriginalException. Ces propriétés ont la valeur null/zéro pour les appels aux méthodes d’interception qui sont appelées avant l’exécution de l’opération, c’est-à-dire pour le... Exécution des méthodes. Si l’opération est exécutée et réussit, result et OriginalResult sont définis sur le résultat de l’opération. Ces valeurs peuvent ensuite être observées dans les méthodes d’interception qui sont appelées après l’exécution de l’opération, c’est-à-dire sur le... Méthodes exécutées. De même, si l’opération lève, les propriétés exception et OriginalException sont définies.  

#### <a name="suppressing-execution"></a>Suppression de l’exécution  

Si un intercepteur définit la propriété Result avant l’exécution de la commande (dans l’un des... Exécution des méthodes) ensuite, EF ne tente pas d’exécuter la commande, mais utilise simplement le jeu de résultats. En d’autres termes, l’intercepteur peut supprimer l’exécution de la commande, mais l’EF continue comme si la commande avait été exécutée.  

Un exemple d’utilisation de cette méthode est le traitement par lot de commandes qui a traditionnellement été fait avec un fournisseur d’encapsulation. L’intercepteur stocke la commande en vue d’une exécution ultérieure sous la forme d’un lot, mais prétend « prétendre » à EF que la commande a été exécutée normalement. Notez qu’il faut plus que cela pour implémenter le traitement par lot, mais il s’agit d’un exemple de la façon dont la modification du résultat de l’interception peut être utilisée.  

L’exécution peut également être supprimée en définissant la propriété d’exception dans l’un des... Exécution des méthodes. EF se poursuit alors comme si l’exécution de l’opération avait échoué en levant l’exception donnée. Cela peut, bien sûr, provoquer le blocage de l’application, mais il peut également s’agir d’une exception temporaire ou d’une autre exception gérée par EF. Par exemple, il peut être utilisé dans les environnements de test pour tester le comportement d’une application lorsque l’exécution de la commande échoue.  

#### <a name="changing-the-result-after-execution"></a>Modification du résultat après l’exécution  

Si un intercepteur définit la propriété Result après l’exécution de la commande (dans l’un des... Méthodes exécutées) ensuite, EF utilise le résultat modifié au lieu du résultat qui a été réellement retourné par l’opération. De même, si un intercepteur définit la propriété d’exception après l’exécution de la commande, EF lèvera l’exception Set comme si l’opération avait levé l’exception.  

Un intercepteur peut également définir la propriété d’exception sur null pour indiquer qu’aucune exception ne doit être levée. Cela peut être utile si l’exécution de l’opération a échoué, mais que l’intercepteur souhaite que EF continue comme si l’opération avait réussi. Cela implique généralement de définir le résultat afin que EF ait une valeur de résultat à utiliser lorsqu’il continue.  

#### <a name="originalresult-and-originalexception"></a>OriginalResult et OriginalException  

Une fois que EF a exécuté une opération, il définit les propriétés result et OriginalResult si l’exécution n’a pas échoué ou les propriétés exception et OriginalException si l’exécution a échoué avec une exception.  

Les propriétés OriginalResult et OriginalException sont en lecture seule et ne sont définies par EF qu’après l’exécution d’une opération. Ces propriétés ne peuvent pas être définies par des intercepteurs. Cela signifie qu’un intercepteur peut faire la distinction entre une exception ou un résultat qui a été défini par un autre intercepteur, par opposition à l’exception réelle ou au résultat qui s’est produit lors de l’exécution de l’opération.  

### <a name="registering-interceptors"></a>Inscription des intercepteurs  

Une fois qu’une classe qui implémente une ou plusieurs interfaces d’interception a été créée, elle peut être inscrite auprès d’EF à l’aide de la classe DbInterception. Par exemple :  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

Les intercepteurs peuvent également être inscrits au niveau du domaine d’application à l’aide du mécanisme de configuration basé sur le code DbConfiguration.  

### <a name="example-logging-to-nlog"></a>Exemple : journalisation dans NLog  

Nous allons rassembler tout cela dans un exemple qui utilise IDbCommandInterceptor et [nlog](https://nlog-project.org/) pour :  

- Consigne un avertissement pour toute commande exécutée de façon non asynchrone  
- Consigner une erreur pour toute commande qui lève une exception quand elle est exécutée  

Voici la classe qui effectue la journalisation, qui doit être enregistrée comme indiqué ci-dessus :  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

Notez que ce code utilise le contexte d’interception pour découvrir quand une commande est exécutée de façon non asynchrone et pour découvrir quand une erreur s’est produite lors de l’exécution d’une commande.  
