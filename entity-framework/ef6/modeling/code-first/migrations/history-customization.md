---
title: Personnalisation de la table de l’historique des migrations-EF6
description: Personnalisation de la table d’historique des migrations dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: b71ac7635d6804af2db9bf00acf7e8f4b2a11033
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073459"
---
# <a name="customizing-the-migrations-history-table"></a>Personnalisation de la table d’historique des migrations
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

> [!NOTE]
> Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base. Si ce n’est pas le cas, vous devez lire [migrations code First](xref:ef6/modeling/code-first/migrations/index) avant de continuer.

## <a name="what-is-migrations-history-table"></a>Qu’est-ce que la table historique des migrations ?

La table d’historique des migrations est une table utilisée par Migrations Code First pour stocker des détails sur les migrations appliquées à la base de données. Par défaut, le nom de la table dans la base de données est \_ \_ MigrationHistory et il est créé lors de l’application de la première migration à la base de données. Dans Entity Framework 5, cette table était une table système si l’application utilisait la base de données Microsoft SQL Server. Cela a changé dans le Entity Framework 6, mais la table d’historique des migrations n’est plus marquée comme une table système.

## <a name="why-customize-migrations-history-table"></a>Pourquoi personnaliser la table d’historique des migrations ?

La table d’historique des migrations est supposée être utilisée uniquement par Migrations Code First et la modifier manuellement peut rompre les migrations. Toutefois, parfois, la configuration par défaut n’est pas appropriée et la table doit être personnalisée, par exemple :

-   Vous devez modifier les noms et/ou les facettes des colonnes pour activer un fournisseur de migrations<sup>tiers.</sup>
-   Vous souhaitez modifier le nom de la table
-   Vous devez utiliser un schéma non défini par défaut pour la \_ \_ table MigrationHistory
-   Vous devez stocker des données supplémentaires pour une version donnée du contexte et, par conséquent, vous devez ajouter une colonne supplémentaire à la table.

## <a name="words-of-precaution"></a>Mots de précaution

La modification de la table de l’historique de migration est puissante, mais vous devez veiller à ne pas la abuser. Actuellement, le runtime EF ne vérifie pas si la table d’historique des migrations personnalisée est compatible avec le Runtime. Si ce n’est pas le cas, votre application peut s’arrêter au moment de l’exécution ou se comporter de façon imprévisible. Cela est encore plus important si vous utilisez plusieurs contextes par base de données, auquel cas plusieurs contextes peuvent utiliser la même table d’historique de migration pour stocker des informations sur les migrations.

## <a name="how-to-customize-migrations-history-table"></a>Comment personnaliser la table d’historique des migrations ?

Avant de commencer, vous devez savoir que vous pouvez personnaliser la table d’historique des migrations avant d’appliquer la première migration. Maintenant, dans le code.

Tout d’abord, vous devrez créer une classe dérivée de la classe System. Data. Entity. migrations. History. HistoryContext. La classe HistoryContext est dérivée de la classe DbContext, de sorte que la configuration de la table d’historique des migrations est très similaire à la configuration des modèles EF avec l’API Fluent. Il vous suffit de remplacer la méthode OnModelCreating et d’utiliser l’API Fluent pour configurer la table.

>[!NOTE]
> En général, lorsque vous configurez des modèles EF, vous n’avez pas besoin d’appeler de base. OnModelCreating () de la méthode OnModelCreating remplacée puisque DbContext. OnModelCreating () a un corps vide. Ce n’est pas le cas lors de la configuration de la table d’historique des migrations. Dans ce cas, la première chose à faire dans votre remplacement OnModelCreating () consiste à appeler la base. OnModelCreating (). Cela permet de configurer la table d’historique des migrations de la manière par défaut, que vous Affinez ensuite dans la méthode de substitution.

Supposons que vous souhaitez renommer la table d’historique des migrations et la placer dans un schéma personnalisé appelé « admin ». En outre, votre administrateur de bases de donnes souhaite que vous renommez la colonne MigrationId en ID de migration \_ . Pour ce faire, vous pouvez créer la classe suivante dérivée de HistoryContext :

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

Une fois votre HistoryContext personnalisé prêt, vous devez faire en sorte qu’EF le prenne en compte en l’inscrivant via [une configuration basée sur le code](https://msdn.com/data/jj680699):

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

C’est très bien. Vous pouvez maintenant accéder à la console du gestionnaire de package, activer-migrations, ajouter la migration et enfin mettre à jour-base de données. Cela doit entraîner l’ajout à la base de données d’une table d’historique des migrations configurée en fonction des détails que vous avez spécifiés dans votre classe dérivée HistoryContext.

![Table d’historique des migrations](~/ef6/media/database.png)
