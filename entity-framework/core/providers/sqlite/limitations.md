---
title: Fournisseur de base de données SQLite-limitations-EF Core
description: Limitations du fournisseur de base de données Entity Framework Core SQLite par rapport aux autres fournisseurs
author: bricelam
ms.date: 09/24/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 3d696474d401e8fd6c26a78067d292f0bb97a457
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062735"
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

Le `Decimal` type fournit un niveau élevé de précision. Toutefois, si vous n’avez pas besoin de ce niveau de précision, nous vous recommandons d’utiliser à la place un double. Vous pouvez utiliser un [convertisseur de valeur](xref:core/modeling/value-conversions) pour continuer à utiliser Decimal dans vos classes.

```csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Limitations des migrations

Le moteur de base de données SQLite ne prend pas en charge un certain nombre d’opérations de schéma prises en charge par la plupart des autres bases de données relationnelles. Si vous tentez d’appliquer l’une des opérations non prises en charge à une base de données SQLite, une `NotSupportedException` exception est levée.

Une régénération sera tentée afin d’effectuer certaines opérations. Les reconstructions sont possibles uniquement pour les artefacts de base de données qui font partie de votre modèle de EF Core. Si un artefact de base de données ne fait pas partie du modèle, par exemple, s’il a été créé manuellement à l’intérieur d’une migration, alors une `NotSupportedException` est toujours levée.

| Opération            | Pris en charge ?  | Version requise |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (régénération) | 5.0              |
| AddColumn            | ✔           |                  |
| AddForeignKey        | ✔ (régénération) | 5.0              |
| AddPrimaryKey        | ✔ (régénération) | 5.0              |
| AddUniqueConstraint  | ✔ (régénération) | 5.0              |
| AlterColumn          | ✔ (régénération) | 5.0              |
| CreateIndex          | ✔           |                  |
| CreateTable          | ✔           |                  |
| DropCheckConstraint  | ✔ (régénération) | 5.0              |
| DropColumn           | ✔ (régénération) | 5.0              |
| DropForeignKey       | ✔ (régénération) | 5.0              |
| DROP index            | ✔           |                  |
| DropPrimaryKey       | ✔ (régénération) | 5.0              |
| DropTable            | ✔           |                  |
| DropUniqueConstraint | ✔ (régénération) | 5.0              |
| RenameColumn         | ✔           | 2.2              |
| RenameIndex          | ✔ (régénération) |                  |
| RenameTable          | ✔           |                  |
| EnsureSchema         | ✔ (aucune opération)   |                  |
| DropSchema           | ✔ (aucune opération)   |                  |
| Insérer               | ✔           |                  |
| Update               | ✔           |                  |
| Supprimer               | ✔           |                  |

### <a name="migrations-limitations-workaround"></a>Solution de contournement des limitations des migrations

Vous pouvez contourner certaines de ces limitations en écrivant manuellement du code dans vos migrations pour effectuer une reconstruction. Les reconstructions de tables impliquent la création d’une nouvelle table, la copie de données dans la nouvelle table, la suppression de l’ancienne table et la modification du nom de la nouvelle table. Vous devrez utiliser la `Sql(string)` méthode pour effectuer certaines de ces étapes.

Pour plus d’informations, consultez [création d’autres types de modifications de schéma de table](https://sqlite.org/lang_altertable.html#otheralter) dans la documentation sqlite.

### <a name="idempotent-script-limitations"></a>Limitations des scripts idempotent

Contrairement aux autres bases de données, SQLite n’inclut pas de langage procédural. Pour cette raison, il n’existe aucun moyen de générer la logique si-Then requise par les scripts de migration idempotent.

Si vous connaissez la dernière migration appliquée à une base de données, vous pouvez générer un script à partir de cette migration vers la dernière migration.

```dotnetcli
dotnet ef migrations script CurrentMigration
```

Dans le cas contraire, nous vous recommandons `dotnet ef database update` d’utiliser pour appliquer des migrations. À partir de EF Core 5,0, vous pouvez spécifier le fichier de base de données lors de l’exécution de la commande.

```dotnetcli
dotnet ef database update --connection "Data Source=My.db"
```

## <a name="see-also"></a>Voir également

* [Limitations de Microsoft. Data. sqlite Async](/dotnet/standard/data/sqlite/async)
* [Limitations de Microsoft. Data. sqlite ADO.NET](/dotnet/standard/data/sqlite/adonet-limitations)
