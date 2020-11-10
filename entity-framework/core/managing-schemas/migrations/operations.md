---
title: Opérations sur les migrations personnalisées-EF Core
description: Gestion des migrations SQL personnalisées et brutes pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429830"
---
# <a name="custom-migrations-operations"></a>Opérations de migration personnalisées

L’API MigrationBuilder vous permet d’effectuer de nombreux types d’opérations différents au cours d’une migration, mais elle est loin d’être exhaustive. Toutefois, l’API est également extensible, ce qui vous permet de définir vos propres opérations. Il existe deux façons d’étendre l’API : à l’aide de la `Sql()` méthode, ou en définissant des `MigrationOperation` objets personnalisés.

Pour illustrer cela, examinons l’implémentation d’une opération qui crée un utilisateur de base de données à l’aide de chaque approche. Dans nos migrations, nous souhaitons activer l’écriture du code suivant :

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Utilisation de MigrationBuilder. SQL ()

Le moyen le plus simple d’implémenter une opération personnalisée consiste à définir une méthode d’extension qui appelle `MigrationBuilder.Sql()` . Voici un exemple qui génère le Transact-SQL approprié.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> Utilisez la `EXEC` fonction quand une instruction doit être la première ou une seule dans un lot SQL. Il peut également être nécessaire de contourner les erreurs de l’analyseur dans des scripts de migration idempotent qui peuvent se produire lorsque des colonnes référencées n’existent pas actuellement sur une table.

Si vos migrations doivent prendre en charge plusieurs fournisseurs de bases de données, vous pouvez utiliser la `MigrationBuilder.ActiveProvider` propriété. Voici un exemple qui prend en charge à la fois Microsoft SQL Server et PostgreSQL.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

Cette approche fonctionne uniquement si vous connaissez tous les fournisseurs où votre opération personnalisée sera appliquée.

## <a name="using-a-migrationoperation"></a>Utilisation d’un MigrationOperation

Pour découpler l’opération personnalisée de SQL, vous pouvez définir votre propre opération `MigrationOperation` pour la représenter. L’opération est ensuite transmise au fournisseur afin de pouvoir déterminer le SQL approprié à générer.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

Avec cette approche, la méthode d’extension doit simplement ajouter l’une de ces opérations à `MigrationBuilder.Operations` .

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

Cette approche nécessite que chaque fournisseur sache comment générer SQL pour cette opération dans son `IMigrationsSqlGenerator` service. Voici un exemple qui remplace le générateur de SQL Server pour gérer la nouvelle opération.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

Remplacez le service SQL Generator des migrations par défaut par le service mis à jour.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
