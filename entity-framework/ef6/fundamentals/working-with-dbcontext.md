---
title: Utilisation de DbContext-EF6
description: Utilisation de DbContext dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 83f3f318c6cd1b0ea440bd472a651ff4454716f1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072146"
---
# <a name="working-with-dbcontext"></a>Utilisation de DbContext

Pour pouvoir utiliser Entity Framework pour interroger, insérer, mettre à jour et supprimer des données à l’aide d’objets .NET, vous devez d’abord [créer un modèle](xref:ef6/modeling/index) qui mappe les entités et les relations définies dans votre modèle aux tables d’une base de données.

Une fois que vous disposez d’un modèle, la classe principale avec laquelle votre application interagit est `System.Data.Entity.DbContext` (souvent appelée classe de contexte). Vous pouvez utiliser un DbContext associé à un modèle pour effectuer les opérations suivantes :
- Écrire et exécuter des requêtes   
- Matérialiser les résultats d’une requête en tant qu’objets entité
- Suivre les modifications apportées à ces objets
- Conserver les modifications des objets sur la base de données
- Lier des objets en mémoire à des contrôles d’interface utilisateur

Cette page fournit des conseils sur la façon de gérer la classe de contexte.  

## <a name="defining-a-dbcontext-derived-class"></a>Définition d’une classe dérivée DbContext  

La méthode recommandée pour utiliser le contexte consiste à définir une classe qui dérive de DbContext et expose des propriétés DbSet qui représentent des collections des entités spécifiées dans le contexte. Si vous utilisez le concepteur EF, le contexte est généré pour vous. Si vous utilisez Code First, vous écrivez généralement le contexte vous-même.  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

Une fois que vous avez un contexte, vous pouvez rechercher, ajouter (à l’aide des `Add` `Attach` méthodes ou) ou supprimer (à l’aide `Remove` de) des entités dans le contexte par le biais de ces propriétés. L’accès à une `DbSet` propriété sur un objet de contexte représente une requête de démarrage qui retourne toutes les entités du type spécifié. Notez que l’accès à une propriété n’exécute pas la requête. Une requête est exécutée dans les cas suivants :  

- elle est énumérée par une instruction `foreach` (C#) ou `For Each` (Visual Basic) ;  
- Elle est énumérée par une opération de collection telle que `ToArray` , `ToDictionary` ou `ToList` .  
- Les opérateurs LINQ, tels que `First` ou, `Any` sont spécifiés dans la partie la plus à l’extérieur de la requête.  
- L’une des méthodes suivantes est appelée : la `Load` méthode d’extension, `DbEntityEntry.Reload` ,  `Database.ExecuteSqlCommand` et `DbSet<T>.Find` , si une entité avec la clé spécifiée est introuvable déjà chargée dans le contexte.  

## <a name="lifetime"></a>Durée de vie  

La durée de vie du contexte commence lorsque l’instance est créée et se termine lorsque l’instance est supprimée ou récupérée par le garbage collector. Utilisez **si vous** souhaitez que toutes les ressources qui contrôlent le contexte soient supprimées à la fin du bloc. Lorsque vous utilisez **, le**compilateur crée automatiquement un bloc try/finally et appelle dispose dans le bloc **finally** .  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

Voici quelques recommandations générales pour décider de la durée de vie du contexte :  

- Lorsque vous travaillez avec des applications Web, utilisez une instance de contexte par demande.  
- Lorsque vous travaillez avec Windows Presentation Foundation (WPF) ou Windows Forms, utilisez une instance de contexte par formulaire. Cela vous permet d’utiliser la fonctionnalité de suivi des modifications fournie par le contexte.  
- Si l’instance de contexte est créée par un conteneur d’injection de dépendance, il incombe généralement au conteneur de supprimer le contexte.
- Si le contexte est créé dans le code de l’application, n’oubliez pas de supprimer le contexte lorsqu’il n’est plus nécessaire.  
- Lorsque vous travaillez avec un contexte de longue durée, tenez compte des points suivants :  
    - Lorsque vous chargez plus d’objets et leurs références dans la mémoire, la consommation de mémoire du contexte peut augmenter rapidement. Cela peut provoquer des problèmes de performances.  
    - Le contexte n’est pas thread-safe. par conséquent, il ne doit pas être partagé entre plusieurs threads qui y travaillent simultanément.
    - Si une exception fait que le contexte est dans un État irrécupérable, l’application entière peut se terminer.  
    - Les risques liés à l'exécution dans un état d'accès concurrentiel augmentent à mesure que l'intervalle entre le moment où les données sont interrogées et mises à jour s'allonge.  

## <a name="connections"></a>Connexions  

Par défaut, le contexte gère les connexions à la base de données. Le contexte ouvre et ferme les connexions en fonction des besoins. Par exemple, le contexte ouvre une connexion pour exécuter une requête, puis ferme la connexion lorsque tous les jeux de résultats ont été traités.  

Dans certains cas, vous souhaiterez avoir davantage de contrôle sur l'ouverture et la fermeture des connexions. Par exemple, lors de l’utilisation de SQL Server Compact, il est souvent recommandé de conserver une connexion ouverte distincte à la base de données pendant toute la durée de vie de l’application afin d’améliorer les performances. Vous pouvez gérer ce processus manuellement à l'aide de la propriété `Connection`.  
