---
title: 'Fonctionnement des requêtes : EF Core'
description: Informations générales sur la façon dont Entity Framework Core compile et exécute des requêtes en interne
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e0e489f5f797b6f7d8f4860539a538dba90bd1c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616367"
---
# <a name="how-queries-work"></a><span data-ttu-id="cca82-103">Fonctionnement des requêtes</span><span class="sxs-lookup"><span data-stu-id="cca82-103">How Queries Work</span></span>

<span data-ttu-id="cca82-104">Entity Framework Core utilise LINQ (Language Integrated Query) pour interroger les données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cca82-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="cca82-105">LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées en fonction de votre contexte dérivé et de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="cca82-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="cca82-106">La durée de vie d’une requête</span><span class="sxs-lookup"><span data-stu-id="cca82-106">The life of a query</span></span>

<span data-ttu-id="cca82-107">La description suivante est une vue d’ensemble du processus de chaque requête.</span><span class="sxs-lookup"><span data-stu-id="cca82-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="cca82-108">La requête LINQ est traitée par Entity Framework Core pour générer une représentation qui est prête à être traitée par le fournisseur de base de données</span><span class="sxs-lookup"><span data-stu-id="cca82-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="cca82-109">Le résultat est mis en cache afin que ce traitement n’ait pas besoin d’être effectué chaque fois que la requête est exécutée</span><span class="sxs-lookup"><span data-stu-id="cca82-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="cca82-110">Le résultat est transmis au fournisseur de base de données</span><span class="sxs-lookup"><span data-stu-id="cca82-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="cca82-111">Le fournisseur de base de données identifie les parties de la requête qui peuvent être évaluées dans la base de données</span><span class="sxs-lookup"><span data-stu-id="cca82-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="cca82-112">Ces parties de la requête sont traduites en langage de requête spécifique à la base de données (par exemple, SQL pour une base de données relationnelle).</span><span class="sxs-lookup"><span data-stu-id="cca82-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="cca82-113">Une requête est envoyée à la base de données et le jeu de résultats est retourné (les résultats sont des valeurs de la base de données, et non des instances d’entité)</span><span class="sxs-lookup"><span data-stu-id="cca82-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="cca82-114">Pour chaque élément du jeu de résultats</span><span class="sxs-lookup"><span data-stu-id="cca82-114">For each item in the result set</span></span>
   1. <span data-ttu-id="cca82-115">Si la requête est une requête de suivi, EF vérifie si les données représentent une entité déjà présente dans le dispositif de suivi des modifications pour l’instance de contexte</span><span class="sxs-lookup"><span data-stu-id="cca82-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="cca82-116">Dans ce cas, l’entité existante est retournée</span><span class="sxs-lookup"><span data-stu-id="cca82-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="cca82-117">Si ce n’est pas le cas, une nouvelle entité est créée, le suivi des modifications est configuré et la nouvelle entité est retournée.</span><span class="sxs-lookup"><span data-stu-id="cca82-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="cca82-118">Si la requête est une requête sans suivi, une nouvelle entité est toujours créée et retournée</span><span class="sxs-lookup"><span data-stu-id="cca82-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="cca82-119">Lorsque des requêtes sont exécutées</span><span class="sxs-lookup"><span data-stu-id="cca82-119">When queries are executed</span></span>

<span data-ttu-id="cca82-120">Lorsque vous appelez des opérateurs LINQ, vous créez simplement une représentation en mémoire de la requête.</span><span class="sxs-lookup"><span data-stu-id="cca82-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="cca82-121">La requête est envoyée à la base de données uniquement lorsque les résultats sont consommés.</span><span class="sxs-lookup"><span data-stu-id="cca82-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="cca82-122">Les opérations qui génèrent les requêtes envoyées à la base de données les plus courantes sont :</span><span class="sxs-lookup"><span data-stu-id="cca82-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="cca82-123">Itération des résultats dans une boucle `for`</span><span class="sxs-lookup"><span data-stu-id="cca82-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="cca82-124">Utilisation d’un opérateur tel que,,, `ToList` `ToArray` `Single` `Count` ou les surcharges Async équivalentes</span><span class="sxs-lookup"><span data-stu-id="cca82-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="cca82-125">**Toujours valider l’entrée d’utilisateur :** bien qu’EF Core protège contre les attaques par injection SQL au moyen de paramètres et de l’échappement des littéraux dans les requêtes, il ne valide pas les entrées.</span><span class="sxs-lookup"><span data-stu-id="cca82-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="cca82-126">La validation appropriée, conformément aux exigences de l’application, doit être effectuée avant que les valeurs de sources non approuvées ne soient utilisées dans les requêtes LINQ, affectées aux propriétés d’entité ou transmises à d’autres API de EF Core.</span><span class="sxs-lookup"><span data-stu-id="cca82-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="cca82-127">Cela inclut toute entrée utilisateur utilisée pour construire des requêtes de façon dynamique.</span><span class="sxs-lookup"><span data-stu-id="cca82-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="cca82-128">Même si vous utilisez LINQ, si vous acceptez les entrées d’utilisateur pour générer des expressions, vous devez vous assurer que seules les expressions prévues peuvent être construites.</span><span class="sxs-lookup"><span data-stu-id="cca82-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
