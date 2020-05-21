---
title: Fournisseur de base de données SQLite-limitations-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672775"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>Limites d’un fournisseur de base de données EF Core SQLite

Le fournisseur SQLite a un certain nombre de limitations de migrations. La plupart de ces limitations résultent de limitations dans le moteur de base de données SQLite sous-jacent et ne sont pas spécifiques à EF.

## <a name="modeling-limitations"></a>Limitations de la modélisation

La bibliothèque relationnelle commune (partagée par Entity Framework fournisseurs de bases de données relationnelles) définit des API pour les concepts de modélisation communs à la plupart des moteurs de base de données relationnelle. Quelques-uns de ces concepts ne sont pas pris en charge par le fournisseur SQLite.

* Schémas
* Séquences

## <a name="query-limitations"></a>Limitations des requêtes

SQLite ne prend pas en charge en mode natif les types de données suivants. EF Core pouvez lire et écrire des valeurs de ces types, et l’interrogation de l’égalité ( `where e.Property == value` ) est également prise en charge. Toutefois, d’autres opérations, comme la comparaison et le tri, nécessitent une évaluation sur le client.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

Au lieu de `DateTimeOffset` , nous vous recommandons d’utiliser des valeurs DateTime. Lors de la gestion de plusieurs fuseaux horaires, nous vous recommandons de convertir les valeurs en temps UTC avant de les enregistrer, puis de les reconvertir dans le fuseau horaire approprié.

Le `Decimal` type fournit un niveau élevé de précision. Toutefois, si vous n’avez pas besoin de ce niveau de précision, nous vous recommandons d’utiliser à la place un double. Vous pouvez utiliser un [convertisseur de valeur](../../modeling/value-conversions.md) pour continuer à utiliser Decimal dans vos classes.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Limitations des migrations

Le moteur de base de données SQLite ne prend pas en charge un certain nombre d’opérations de schéma prises en charge par la plupart des autres bases de données relationnelles. Si vous tentez d’appliquer l’une des opérations non prises en charge à une base de données SQLite, une `NotSupportedException` exception est levée.

| Opération            | Pris en charge ? | Version requise |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DROP index            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✔          | 2.2.2            |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔ (aucune opération)  | 2.0              |
| DropSchema           | ✔ (aucune opération)  | 2.0              |
| Insérer               | ✔          | 2.0              |
| Update               | ✔          | 2.0              |
| Supprimer               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>Solution de contournement des limitations des migrations

Vous pouvez contourner certaines de ces limitations en écrivant manuellement du code dans vos migrations pour effectuer une reconstruction de table. Une reconstruction de la table implique la modification du nom de la table existante, la création d’une nouvelle table, la copie des données vers la nouvelle table et la suppression de l’ancienne table. Vous devrez utiliser la `Sql(string)` méthode pour effectuer certaines de ces étapes.

Pour plus d’informations, consultez [création d’autres types de modifications de schéma de table](https://sqlite.org/lang_altertable.html#otheralter) dans la documentation sqlite.

À l’avenir, EF peut prendre en charge certaines de ces opérations à l’aide de l’approche de reconstruction de table en coulisses. Vous pouvez [suivre cette fonctionnalité sur notre projet GitHub](https://github.com/aspnet/EntityFrameworkCore/issues/329).
