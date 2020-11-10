---
title: 'Fonctionnement des requêtes : EF Core'
description: Informations générales sur la façon dont Entity Framework Core compile et exécute des requêtes en interne
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: 7b3014cf64f8467ccbec10598ea1bb47304dfe43
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430467"
---
# <a name="how-queries-work"></a>Fonctionnement des requêtes

Entity Framework Core utilise LINQ (Language Integrated Query) pour interroger les données de la base de données. LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées en fonction de votre contexte dérivé et de vos classes d’entité.

> [!NOTE]
> Cet article est obsolète et certaines parties de celui-ci doivent être mises à jour pour tenir compte des modifications apportées à la conception du pipeline de requête. Si vous avez des doutes sur les comportements mentionnés ici, [Posez une question](https://github.com/dotnet/efcore/issues/new/choose).

## <a name="the-life-of-a-query"></a>La durée de vie d’une requête

La description suivante est une vue d’ensemble du processus de chaque requête.

1. La requête LINQ est traitée par Entity Framework Core pour générer une représentation qui est prête à être traitée par le fournisseur de base de données
   1. Le résultat est mis en cache afin que ce traitement n’ait pas besoin d’être effectué chaque fois que la requête est exécutée
2. Le résultat est transmis au fournisseur de base de données
   1. Le fournisseur de base de données identifie les parties de la requête qui peuvent être évaluées dans la base de données
   2. Ces parties de la requête sont traduites en langage de requête spécifique à la base de données (par exemple, SQL pour une base de données relationnelle).
   3. Une requête est envoyée à la base de données et le jeu de résultats est retourné (les résultats sont des valeurs de la base de données, et non des instances d’entité)
3. Pour chaque élément du jeu de résultats
   1. Si la requête est une requête de suivi, EF vérifie si les données représentent une entité déjà présente dans le dispositif de suivi des modifications pour l’instance de contexte
      * Dans ce cas, l’entité existante est retournée
      * Si ce n’est pas le cas, une nouvelle entité est créée, le suivi des modifications est configuré et la nouvelle entité est retournée.
   2. Si la requête est une requête sans suivi, une nouvelle entité est toujours créée et retournée

## <a name="when-queries-are-executed"></a>Lorsque des requêtes sont exécutées

Lorsque vous appelez des opérateurs LINQ, vous créez simplement une représentation en mémoire de la requête. La requête est envoyée à la base de données uniquement lorsque les résultats sont consommés.

Les opérations qui génèrent les requêtes envoyées à la base de données les plus courantes sont :

* Itération des résultats dans une boucle `for`
* Utilisation d’un opérateur tel que,,, `ToList` `ToArray` `Single` `Count` ou les surcharges Async équivalentes

> [!WARNING]  
> **Toujours valider l’entrée d’utilisateur :** bien qu’EF Core protège contre les attaques par injection SQL au moyen de paramètres et de l’échappement des littéraux dans les requêtes, il ne valide pas les entrées. La validation appropriée, conformément aux exigences de l’application, doit être effectuée avant que les valeurs de sources non approuvées ne soient utilisées dans les requêtes LINQ, affectées aux propriétés d’entité ou transmises à d’autres API de EF Core. Cela inclut toute entrée utilisateur utilisée pour construire des requêtes de façon dynamique. Même si vous utilisez LINQ, si vous acceptez les entrées d’utilisateur pour générer des expressions, vous devez vous assurer que seules les expressions prévues peuvent être construites.
