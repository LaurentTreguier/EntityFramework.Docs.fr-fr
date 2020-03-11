---
title: Personnalisation de la table de l’historique des migrations-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418978"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="e9f38-102">Personnalisation de la table d’historique des migrations</span><span class="sxs-lookup"><span data-stu-id="e9f38-102">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="e9f38-103">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="e9f38-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e9f38-104">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="e9f38-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="e9f38-105">Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base.</span><span class="sxs-lookup"><span data-stu-id="e9f38-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="e9f38-106">Si ce n’est pas le cas, vous devez lire [migrations code First](~/ef6/modeling/code-first/migrations/index.md) avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="e9f38-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="e9f38-107">Qu’est-ce que la table historique des migrations ?</span><span class="sxs-lookup"><span data-stu-id="e9f38-107">What is Migrations History Table?</span></span>

<span data-ttu-id="e9f38-108">La table d’historique des migrations est une table utilisée par Migrations Code First pour stocker des détails sur les migrations appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="e9f38-108">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="e9f38-109">Par défaut, le nom de la table dans la base de données est \_\_MigrationHistory et il est créé lors de l’application de la première migration à la base de données.</span><span class="sxs-lookup"><span data-stu-id="e9f38-109">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="e9f38-110">Dans Entity Framework 5, cette table était une table système si l’application utilisait la base de données Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e9f38-110">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="e9f38-111">Cela a changé dans le Entity Framework 6, mais la table d’historique des migrations n’est plus marquée comme une table système.</span><span class="sxs-lookup"><span data-stu-id="e9f38-111">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="e9f38-112">Pourquoi personnaliser la table d’historique des migrations ?</span><span class="sxs-lookup"><span data-stu-id="e9f38-112">Why customize Migrations History Table?</span></span>

<span data-ttu-id="e9f38-113">La table d’historique des migrations est supposée être utilisée uniquement par Migrations Code First et la modifier manuellement peut rompre les migrations.</span><span class="sxs-lookup"><span data-stu-id="e9f38-113">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="e9f38-114">Toutefois, parfois, la configuration par défaut n’est pas appropriée et la table doit être personnalisée, par exemple :</span><span class="sxs-lookup"><span data-stu-id="e9f38-114">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="e9f38-115">Vous devez modifier les noms et/ou les facettes des colonnes pour activer un fournisseur de migrations<sup>tiers.</sup></span><span class="sxs-lookup"><span data-stu-id="e9f38-115">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="e9f38-116">Vous souhaitez modifier le nom de la table</span><span class="sxs-lookup"><span data-stu-id="e9f38-116">You want to change the name of the table</span></span>
-   <span data-ttu-id="e9f38-117">Vous devez utiliser un schéma non défini par défaut pour le \_\_table MigrationHistory</span><span class="sxs-lookup"><span data-stu-id="e9f38-117">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="e9f38-118">Vous devez stocker des données supplémentaires pour une version donnée du contexte et, par conséquent, vous devez ajouter une colonne supplémentaire à la table.</span><span class="sxs-lookup"><span data-stu-id="e9f38-118">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="e9f38-119">Mots de précaution</span><span class="sxs-lookup"><span data-stu-id="e9f38-119">Words of precaution</span></span>

<span data-ttu-id="e9f38-120">La modification de la table de l’historique de migration est puissante, mais vous devez veiller à ne pas la abuser.</span><span class="sxs-lookup"><span data-stu-id="e9f38-120">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="e9f38-121">Actuellement, le runtime EF ne vérifie pas si la table d’historique des migrations personnalisée est compatible avec le Runtime.</span><span class="sxs-lookup"><span data-stu-id="e9f38-121">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="e9f38-122">Si ce n’est pas le cas, votre application peut s’arrêter au moment de l’exécution ou se comporter de façon imprévisible.</span><span class="sxs-lookup"><span data-stu-id="e9f38-122">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="e9f38-123">Cela est encore plus important si vous utilisez plusieurs contextes par base de données, auquel cas plusieurs contextes peuvent utiliser la même table d’historique de migration pour stocker des informations sur les migrations.</span><span class="sxs-lookup"><span data-stu-id="e9f38-123">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="e9f38-124">Comment personnaliser la table d’historique des migrations ?</span><span class="sxs-lookup"><span data-stu-id="e9f38-124">How to customize Migrations History Table?</span></span>

<span data-ttu-id="e9f38-125">Avant de commencer, vous devez savoir que vous pouvez personnaliser la table d’historique des migrations avant d’appliquer la première migration.</span><span class="sxs-lookup"><span data-stu-id="e9f38-125">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="e9f38-126">Maintenant, dans le code.</span><span class="sxs-lookup"><span data-stu-id="e9f38-126">Now, to the code.</span></span>

<span data-ttu-id="e9f38-127">Tout d’abord, vous devrez créer une classe dérivée de la classe System. Data. Entity. migrations. History. HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="e9f38-127">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="e9f38-128">La classe HistoryContext est dérivée de la classe DbContext, de sorte que la configuration de la table d’historique des migrations est très similaire à la configuration des modèles EF avec l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="e9f38-128">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="e9f38-129">Il vous suffit de remplacer la méthode OnModelCreating et d’utiliser l’API Fluent pour configurer la table.</span><span class="sxs-lookup"><span data-stu-id="e9f38-129">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="e9f38-130">En général, lorsque vous configurez des modèles EF, vous n’avez pas besoin d’appeler de base. OnModelCreating () de la méthode OnModelCreating remplacée puisque DbContext. OnModelCreating () a un corps vide.</span><span class="sxs-lookup"><span data-stu-id="e9f38-130">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="e9f38-131">Ce n’est pas le cas lors de la configuration de la table d’historique des migrations.</span><span class="sxs-lookup"><span data-stu-id="e9f38-131">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="e9f38-132">Dans ce cas, la première chose à faire dans votre remplacement OnModelCreating () consiste à appeler la base. OnModelCreating ().</span><span class="sxs-lookup"><span data-stu-id="e9f38-132">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="e9f38-133">Cela permet de configurer la table d’historique des migrations de la manière par défaut, que vous Affinez ensuite dans la méthode de substitution.</span><span class="sxs-lookup"><span data-stu-id="e9f38-133">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="e9f38-134">Supposons que vous souhaitez renommer la table d’historique des migrations et la placer dans un schéma personnalisé appelé « admin ».</span><span class="sxs-lookup"><span data-stu-id="e9f38-134">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="e9f38-135">En outre, votre administrateur de bases de donnes souhaite que vous renommez la colonne MigrationId en ID\_de migration.</span><span class="sxs-lookup"><span data-stu-id="e9f38-135">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="e9f38-136"> Pour ce faire, vous pouvez créer la classe suivante dérivée de HistoryContext :</span><span class="sxs-lookup"><span data-stu-id="e9f38-136"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="e9f38-137">Une fois votre HistoryContext personnalisé prêt, vous devez faire en sorte qu’EF le prenne en compte en l’inscrivant via [une configuration basée sur le code](https://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="e9f38-137">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="e9f38-138">C’est très bien.</span><span class="sxs-lookup"><span data-stu-id="e9f38-138">That’s pretty much it.</span></span> <span data-ttu-id="e9f38-139">Vous pouvez maintenant accéder à la console du gestionnaire de package, activer-migrations, ajouter la migration et enfin mettre à jour-base de données.</span><span class="sxs-lookup"><span data-stu-id="e9f38-139">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="e9f38-140">Cela doit entraîner l’ajout à la base de données d’une table d’historique des migrations configurée en fonction des détails que vous avez spécifiés dans votre classe dérivée HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="e9f38-140">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![Base de données](~/ef6/media/database.png)
