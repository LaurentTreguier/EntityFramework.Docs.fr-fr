---
title: Fonctions de base de données-EF Core
description: Informations sur les fonctions de base de données prises en charge dans EF Core la traduction des requêtes
author: smitpatel
ms.date: 11/10/2020
uid: core/querying/database-functions
ms.openlocfilehash: 34ced2a602168f6ed84763c046ef581cb87026e8
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503797"
---
# <a name="database-functions"></a>Fonctions de base de données

Les fonctions de base de données sont l’équivalent de la base de données des [méthodes C#](/dotnet/csharp/programming-guide/classes-and-structs/methods). Une fonction de base de données peut être appelée avec zéro ou plusieurs paramètres et calcule le résultat en fonction des valeurs de paramètre. La plupart des bases de données, qui utilisent SQL pour l’interrogation, prennent en charge les fonctions de base de données. Par conséquent, SQL généré par EF Core la traduction de requête permet également d’appeler des fonctions de base de données. Les méthodes C# n’ont pas besoin de se traduire strictement en fonctions de base de données dans EF Core.

- Une méthode C# ne peut pas avoir une fonction de base de données équivalente.
  - <xref:System.String.IsNullOrEmpty%2A?displayProperty=nameWithType> la méthode se traduit par une vérification de valeur null et une comparaison avec une chaîne vide dans la base de données plutôt qu’une fonction.
  - <xref:System.String.Equals(System.String,System.StringComparison)?displayProperty=nameWithType> la méthode n’a pas d’équivalent de base de données, car la comparaison de chaînes ne peut pas être représentée ou simulée facilement dans une base de données.
- Une fonction de base de données ne peut pas avoir une méthode C# équivalente. L' `??` opérateur en C#, qui n’a pas de méthode, se traduit par la `COALESCE` fonction dans la base de données.

## <a name="types-of-database-functions"></a>Types de fonctions de base de données

EF Core génération SQL prend en charge un sous-ensemble de fonctions qui peuvent être utilisées dans les bases de données. Cette limitation provient de la possibilité de représenter une requête dans LINQ pour la fonction de base de données donnée. En outre, chaque base de données a une prise en charge différente des fonctions de base de données, de sorte que EF Core fournit un sous-ensemble commun. Un fournisseur de base de données est libre d’étendre EF Core génération SQL pour prendre en charge davantage de modèles. Voici les types de fonctions de base de données que EF Core prend en charge et identifie de manière unique. Ces termes permettent également de comprendre les traductions intégrées aux fournisseurs de EF Core.

### <a name="built-in-vs-user-defined-functions"></a>Fonctions intégrées vs définies par l’utilisateur

Les fonctions intégrées sont fournies avec la base de données prédéfinie, mais les fonctions définies par l’utilisateur sont définies explicitement par l’utilisateur dans la base de données. Lorsque EF Core traduit des requêtes pour utiliser des fonctions de base de données, il utilise des fonctions intégrées pour s’assurer que la fonction est toujours disponible dans la base de données. La distinction entre les fonctions intégrées est nécessaire dans certaines bases de données pour générer correctement SQL. Par exemple, SqlServer requiert que chaque fonction définie par l’utilisateur soit appelée avec un nom qualifié par un schéma. Toutefois, les fonctions intégrées dans SqlServer n’ont pas de schéma. PostgreSQL définit la fonction intégrée dans le `public` schéma, mais elles peuvent être appelées avec des noms qualifiés de schéma.

### <a name="aggregate-vs-scalar-vs-table-valued-functions"></a>Fonctions agrégées vs scalaires vs

- Les fonctions scalaires prennent des valeurs scalaires, comme des paramètres ou des chaînes, et retournent une valeur scalaire comme résultat. Les fonctions scalaires peuvent être utilisées n’importe où dans SQL où une valeur scalaire peut être passée.
- Les fonctions d’agrégation prennent un flux de valeurs scalaires en tant que paramètres et retournent une valeur scalaire comme résultat. Les fonctions d’agrégation sont appliquées à l’ensemble du jeu de résultats de la requête ou à un groupe de valeurs générées par l’opérateur Applying `GROUP BY` .
- Les fonctions table acceptent des valeurs scalaires comme paramètres et retournent un flux de lignes en tant que résultat. Les fonctions table sont utilisées comme une clause table source in `FROM` .

### <a name="niladic-functions"></a>Fonctions niladic

Les fonctions niladic sont des fonctions de base de données spéciales qui n’ont pas de paramètres et doivent être appelées sans parenthèses. Elles sont similaires à l’accès aux propriétés/champs sur une instance en C#. Les fonctions niladic diffèrent des fonctions sans paramètre, car cette dernière nécessite des parenthèses vides. Il n’existe pas de nom spécial pour les fonctions de base de données qui nécessitent toujours des parenthèses. Une autre sous-partie des fonctions de base de données basée sur le nombre de paramètres est variadiques functions. Les fonctions variadiques peuvent prendre un nombre variable de paramètres lorsqu’elles sont appelées.

## <a name="database-function-mappings-in-ef-core"></a>Mappages de fonctions de base de données dans EF Core

EF Core prend en charge trois méthodes différentes de mappage entre les fonctions C# et les fonctions de base de données.

### <a name="built-in-function-mapping"></a>Mappage de fonctions intégrées

Par défaut EF Core fournisseurs fournissent des mappages pour diverses fonctions intégrées sur des types primitifs. Par exemple, <xref:System.String.ToLower?displayProperty=nameWithType> se traduit par `LOWER` dans SqlServer. Cette fonctionnalité permet aux utilisateurs d’écrire des requêtes dans LINQ en toute transparence. Nous fournissons généralement une traduction dans la base de données qui donne le même résultat que celui fourni par la fonction C# côté client. Parfois, pour y parvenir, la traduction réelle peut être plus complexe qu’une fonction de base de données. Dans certains scénarios, nous fournissons également la traduction la plus appropriée plutôt que la correspondance de la sémantique C#. La même fonctionnalité est également utilisée pour fournir des traductions communes pour certains des accès aux membres C#. Par exemple, <xref:System.String.Length?displayProperty=nameWithType> se traduit par `LEN` dans SqlServer. Hormis les fournisseurs, les enregistreurs de plug-ins peuvent également ajouter des traductions supplémentaires. Cette extensibilité est utile lorsque les plug-ins ajoutent la prise en charge d’autres types en tant que types primitifs et souhaitent traduire les méthodes sur eux.

### <a name="effunctions-mapping"></a>EF. Mappage des fonctions

Dans la mesure où toutes les fonctions de base de données n’ont pas d’équivalents C#, les fournisseurs EF Core ont des méthodes C# spéciales pour appeler certaines fonctions de base de données. Ces méthodes sont définies comme méthodes d’extension sur `EF.Functions` pour être utilisées dans les requêtes LINQ. Ces méthodes sont spécifiques au fournisseur, car elles sont étroitement liées à des fonctions de base de données particulières. Par conséquent, une méthode qui fonctionne pour un fournisseur ne fonctionnera probablement pas pour un autre fournisseur. En outre, étant donné que l’objectif de ces méthodes est d’appeler une fonction de base de données dans la requête traduite, essayer de les évaluer sur le client entraîne une exception.

### <a name="user-defined-function-mapping"></a>Mappage des fonctions définies par l’utilisateur

Outre les mappages fournis par les fournisseurs de EF Core, les utilisateurs peuvent également définir un mappage personnalisé. Un mappage défini par l’utilisateur étend la traduction des requêtes en fonction des besoins de l’utilisateur. Cette fonctionnalité est utile lorsqu’il existe des fonctions définies par l’utilisateur dans la base de données, que l’utilisateur souhaite appeler à partir de leur requête LINQ.

## <a name="see-also"></a>Voir aussi

- [Mappages de fonctions intégrées SqlServer](xref:core/providers/sql-server/functions)
- [Mappages de fonctions intégrées SQLite](xref:core/providers/sqlite/functions)
- [Mappages de fonctions intégrées Cosmos](xref:core/providers/cosmos/functions)
