---
title: Client et évaluation du serveur-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 5cfb05041f04246712fb699f58b407f70a75ce92
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445968"
---
# <a name="client-vs-server-evaluation"></a>Comparaison entre client et serveur

En règle générale, Entity Framework Core tente d’évaluer autant que possible une requête sur le serveur. EF Core convertit des parties de la requête en paramètres, qu’elle peut évaluer côté client. Le reste de la requête (avec les paramètres générés) est donné au fournisseur de base de données pour déterminer la requête de base de données équivalente à évaluer sur le serveur. EF Core prend en charge l’évaluation partielle du client dans la projection de niveau supérieur (essentiellement, le dernier appel à `Select()`). Si la projection de niveau supérieur dans la requête ne peut pas être traduite sur le serveur, EF Core extrait toutes les données requises du serveur et évalue les autres parties de la requête sur le client. Si EF Core détecte une expression, à un emplacement autre que la projection de niveau supérieur, qui ne peut pas être traduite sur le serveur, elle lève une exception Runtime. Découvrez [Comment fonctionne la requête](xref:core/querying/how-query-works) pour comprendre comment EF Core détermine ce qui ne peut pas être traduit sur le serveur.

> [!NOTE]
> Avant la version 3,0, Entity Framework Core l’évaluation du client prise en charge n’importe où dans la requête. Pour plus d’informations, consultez la [section versions précédentes](#previous-versions).

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) sur GitHub.

## <a name="client-evaluation-in-the-top-level-projection"></a>Évaluation du client dans la projection de niveau supérieur

Dans l’exemple suivant, une méthode d’assistance est utilisée pour normaliser les URL des blogs, qui sont retournées à partir d’une base de données SQL Server. Étant donné que le fournisseur de SQL Server n’a aucune idée de la façon dont cette méthode est implémentée, il n’est pas possible de la traduire en SQL. Tous les autres aspects de la requête sont évalués dans la base de données, mais le fait de passer le @no__t retourné-0 par le biais de cette méthode est effectué sur le client.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>Évaluation du client non prise en charge

Si l’évaluation du client est utile, elle peut entraîner des performances médiocres. Considérez la requête suivante, dans laquelle la méthode d’assistance est désormais utilisée dans un filtre Where. Étant donné que le filtre ne peut pas être appliqué dans la base de données, toutes les données doivent être extraites en mémoire pour appliquer le filtre sur le client. En fonction du filtre et de la quantité de données sur le serveur, l’évaluation du client peut entraîner des performances médiocres. Ainsi Entity Framework Core bloque cette évaluation du client et lève une exception d’exécution.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>Évaluation explicite du client

Vous devrez peut-être forcer l’évaluation du client explicitement dans certains cas, comme suit :

- La quantité de données est faible, de sorte que l’évaluation sur le client n’entraîne pas une baisse considérable des performances.
- L’opérateur LINQ utilisé n’a pas de traduction côté serveur.

Dans ce cas, vous pouvez choisir explicitement l’évaluation du client en appelant des méthodes comme `AsEnumerable` ou `ToList` (`AsAsyncEnumerable` ou `ToListAsync` pour Async). En utilisant `AsEnumerable`, vous transmettrez les résultats, mais l’utilisation de `ToList` entraînerait une mise en mémoire tampon en créant une liste, qui prend également de la mémoire supplémentaire. Toutefois, si vous énumérez plusieurs fois, le stockage des résultats dans une liste est plus utile, car il n’existe qu’une seule requête dans la base de données. En fonction de l’utilisation particulière, vous devez déterminer la méthode qui est plus utile pour le cas.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>Fuite de mémoire potentielle dans l’évaluation du client

Étant donné que la traduction et la compilation des requêtes sont coûteuses, EF Core met en cache le plan de requête compilé. Le délégué mis en cache peut utiliser le code client lors de l’évaluation du client de la projection de niveau supérieur. EF Core génère des paramètres pour les parties évaluées par le client de l’arborescence et réutilise le plan de requête en remplaçant les valeurs des paramètres. Toutefois, certaines constantes de l’arborescence de l’expression ne peuvent pas être converties en paramètres. Si le délégué mis en cache contient des constantes, ces objets ne peuvent pas être récupérés par le garbage collector, car ils sont toujours référencés. Si un tel objet contient un DbContext ou d’autres services qu’il contient, cela peut entraîner une augmentation de l’utilisation de la mémoire de l’application au fil du temps. Ce comportement est généralement le signe d’une fuite de mémoire. EF Core lève une exception chaque fois qu’il s’agit d’une constante d’un type qui ne peut pas être mappé à l’aide du fournisseur de base de données actuel. Les causes courantes et leurs solutions sont les suivantes :

- **Utilisation d’une méthode d’instance**: lors de l’utilisation de méthodes d’instance dans une projection cliente, l’arborescence de l’expression contient une constante de l’instance. Si votre méthode n’utilise pas de données de l’instance, envisagez de rendre la méthode statique. Si vous avez besoin de données d’instance dans le corps de la méthode, transmettez les données spécifiques en tant qu’argument à la méthode.
- **Passage d’arguments de constante à la méthode**: ce cas se produit généralement en utilisant `this` dans un argument de la méthode cliente. Envisagez de fractionner l’argument dans en plusieurs arguments scalaires, qui peuvent être mappés par le fournisseur de base de données.
- **Autres constantes**: si une constante est parvenue dans un autre cas, vous pouvez évaluer si la constante est nécessaire dans le traitement. S’il est nécessaire d’avoir la constante, ou si vous ne pouvez pas utiliser une solution des cas ci-dessus, créez une variable locale pour stocker la valeur et utilisez la variable locale dans la requête. EF Core convertira la variable locale en paramètre.

## <a name="previous-versions"></a>Versions antérieures

La section suivante s’applique aux versions de EF Core antérieures à 3,0.

Les anciennes versions de EF Core prises en charge pour l’évaluation du client dans n’importe quelle partie de la requête, et pas seulement la projection de niveau supérieur. C’est pourquoi les requêtes similaires à une publication sous la section d' [évaluation du client non prise en charge](#unsupported-client-evaluation) fonctionnaient correctement. Dans la mesure où ce comportement peut provoquer des problèmes de performances invisibles, EF Core journalisé un avertissement d’évaluation du client. Pour plus d’informations sur l’affichage de la sortie de journalisation, consultez [journalisation](xref:core/miscellaneous/logging).

Si vous le souhaitez, vous pouvez EF Core modifier le comportement par défaut pour lever une exception ou ne rien faire lors de l’évaluation du client (à l’exception de dans la projection). Le comportement de levée d’exception le rendrait similaire au comportement dans 3,0. Pour modifier le comportement, vous devez configurer des avertissements lors de la configuration des options de votre contexte (généralement dans `DbContext.OnConfiguring`, ou dans `Startup.cs` si vous utilisez ASP.NET Core.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
