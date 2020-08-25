---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3a1f5c7d44ad0e4d648492c4edcf14678c73538e
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847591"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="40c9c-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="40c9c-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="40c9c-104">EF Core 5,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="40c9c-105">Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="40c9c-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="40c9c-106">Cette page ne duplique pas le [plan pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="40c9c-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="40c9c-107">Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="40c9c-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="40c9c-108">Nous ajouterons des liens à partir d’ici à la documentation officielle au fur et à mesure de sa publication.</span><span class="sxs-lookup"><span data-stu-id="40c9c-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="40c9c-109">Version préliminaire 8</span><span class="sxs-lookup"><span data-stu-id="40c9c-109">Preview 8</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="40c9c-110">Mappage table par type (TPT)</span><span class="sxs-lookup"><span data-stu-id="40c9c-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="40c9c-111">Par défaut, EF Core mappe une hiérarchie d’héritage de types .NET à une seule table de base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="40c9c-112">C’est ce que l’on appelle le mappage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="40c9c-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="40c9c-113">EF Core 5,0 permet également de mapper chaque type .NET dans une hiérarchie d’héritage à une autre table de base de données. connu sous le nom de mappage table par type (TPT).</span><span class="sxs-lookup"><span data-stu-id="40c9c-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="40c9c-114">Par exemple, considérez ce modèle avec une hiérarchie mappée :</span><span class="sxs-lookup"><span data-stu-id="40c9c-114">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="40c9c-115">Par défaut, EF Core mappe ce à une table unique :</span><span class="sxs-lookup"><span data-stu-id="40c9c-115">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="40c9c-116">Toutefois, le mappage de chaque type d’entité à une autre table génère à la place une table par type :</span><span class="sxs-lookup"><span data-stu-id="40c9c-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="40c9c-117">Notez que la création de contraintes de clé étrangère indiquées ci-dessus a été ajoutée après la création de branche du code pour Preview 8.</span><span class="sxs-lookup"><span data-stu-id="40c9c-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="40c9c-118">Les types d’entités peuvent être mappés à des tables différentes à l’aide des attributs de mappage :</span><span class="sxs-lookup"><span data-stu-id="40c9c-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="40c9c-119">Ou à l’aide de la `ModelBuilder` Configuration :</span><span class="sxs-lookup"><span data-stu-id="40c9c-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="40c9c-120">La documentation est suivie d’un problème [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="40c9c-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="40c9c-121">Migrations : reconstruire les tables SQLite</span><span class="sxs-lookup"><span data-stu-id="40c9c-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="40c9c-122">Par rapport à d’autres bases de données, SQLite est relativement limité dans ses fonctionnalités de manipulation de schéma.</span><span class="sxs-lookup"><span data-stu-id="40c9c-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="40c9c-123">Par exemple, la suppression d’une colonne d’une table existante nécessite la suppression et la recréation de la table entière.</span><span class="sxs-lookup"><span data-stu-id="40c9c-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="40c9c-124">EF Core migrations 5,0 prend désormais en charge la reconstruction automatique de la table pour les modifications de schéma qui l’exigent.</span><span class="sxs-lookup"><span data-stu-id="40c9c-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="40c9c-125">Par exemple, imaginez que nous disposons d’une `Unicorns` table créée pour un `Unicorn` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="40c9c-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="40c9c-126">Nous avons ensuite appris que le stockage de l’âge d’une licorne est considéré comme très impropre. nous allons donc supprimer cette propriété, ajouter une nouvelle migration et mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="40c9c-127">Cette mise à jour échoue lors de l’utilisation de EF Core 3,1, car la colonne ne peut pas être supprimée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="40c9c-128">Dans EF Core 5,0, les migrations recréent à la place la table :</span><span class="sxs-lookup"><span data-stu-id="40c9c-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="40c9c-129">Notez que :</span><span class="sxs-lookup"><span data-stu-id="40c9c-129">Notice that:</span></span>
* <span data-ttu-id="40c9c-130">Une table temporaire est créée avec le schéma souhaité pour la nouvelle table</span><span class="sxs-lookup"><span data-stu-id="40c9c-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="40c9c-131">Les données sont copiées à partir de la table actuelle dans la table temporaire</span><span class="sxs-lookup"><span data-stu-id="40c9c-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="40c9c-132">Mise hors tension de la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="40c9c-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="40c9c-133">La table actuelle est supprimée</span><span class="sxs-lookup"><span data-stu-id="40c9c-133">The current table is dropped</span></span>
* <span data-ttu-id="40c9c-134">La table temporaire est renommée en une nouvelle table</span><span class="sxs-lookup"><span data-stu-id="40c9c-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="40c9c-135">La documentation est suivie d’un problème [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="40c9c-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="40c9c-136">Fonctions table</span><span class="sxs-lookup"><span data-stu-id="40c9c-136">Table-valued functions</span></span>

<span data-ttu-id="40c9c-137">Cette fonctionnalité a été ajoutée par la communauté [@pmiddleton](https://github.com/pmiddleton) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="40c9c-138">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="40c9c-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="40c9c-139">EF Core 5,0 offre une prise en charge de première classe pour le mappage des méthodes .NET aux fonctions table (TVF).</span><span class="sxs-lookup"><span data-stu-id="40c9c-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="40c9c-140">Ces fonctions peuvent ensuite être utilisées dans les requêtes LINQ où la composition supplémentaire sur les résultats de la fonction sera également traduite en SQL.</span><span class="sxs-lookup"><span data-stu-id="40c9c-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="40c9c-141">Par exemple, considérez cette TVF définie dans une base de données SQL Server :</span><span class="sxs-lookup"><span data-stu-id="40c9c-141">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="40c9c-142">Le modèle de EF Core nécessite deux types d’entités pour utiliser cette TVF :</span><span class="sxs-lookup"><span data-stu-id="40c9c-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="40c9c-143">`Employee`Type qui est mappé à la table Employees de manière normale</span><span class="sxs-lookup"><span data-stu-id="40c9c-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="40c9c-144">`Report`Type qui correspond à la forme retournée par la fonction TVF</span><span class="sxs-lookup"><span data-stu-id="40c9c-144">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="40c9c-145">Ces types doivent être inclus dans le modèle de EF Core :</span><span class="sxs-lookup"><span data-stu-id="40c9c-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="40c9c-146">Notez que n' `Report` a pas de clé primaire et doit donc être configuré en tant que tel.</span><span class="sxs-lookup"><span data-stu-id="40c9c-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="40c9c-147">Enfin, une méthode .NET doit être mappée à la TVF dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="40c9c-148">Cette méthode peut être définie sur DbContext à l’aide de la nouvelle `FromExpression` méthode :</span><span class="sxs-lookup"><span data-stu-id="40c9c-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="40c9c-149">Cette méthode utilise un paramètre et un type de retour qui correspondent à la fonction TVF définie ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="40c9c-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="40c9c-150">La méthode est ensuite ajoutée au modèle EF Core dans OnModelCreating :</span><span class="sxs-lookup"><span data-stu-id="40c9c-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="40c9c-151">(L’utilisation d’une expression lambda est un moyen simple de passer le `MethodInfo` à EF Core.</span><span class="sxs-lookup"><span data-stu-id="40c9c-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="40c9c-152">Les arguments passés à la méthode sont ignorés.)</span><span class="sxs-lookup"><span data-stu-id="40c9c-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="40c9c-153">Nous pouvons maintenant écrire des requêtes qui appellent `GetReports` et composent les résultats.</span><span class="sxs-lookup"><span data-stu-id="40c9c-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="40c9c-154">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-154">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="40c9c-155">Sur SQL Server, cela se traduit par :</span><span class="sxs-lookup"><span data-stu-id="40c9c-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="40c9c-156">Notez que le SQL est enraciné dans la `Employees` table, appelle `GetReports` , puis ajoute une clause WHERE supplémentaire sur les résultats de la fonction.</span><span class="sxs-lookup"><span data-stu-id="40c9c-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="40c9c-157">Mappage de requête/mise à jour flexible</span><span class="sxs-lookup"><span data-stu-id="40c9c-157">Flexible query/update mapping</span></span>

<span data-ttu-id="40c9c-158">EF Core 5,0 permet de mapper le même type d’entité à des objets de base de données différents.</span><span class="sxs-lookup"><span data-stu-id="40c9c-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="40c9c-159">Ces objets peuvent être des tables, des vues ou des fonctions.</span><span class="sxs-lookup"><span data-stu-id="40c9c-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="40c9c-160">Par exemple, un type d’entité peut être mappé à une vue de base de données et à une table de base de données :</span><span class="sxs-lookup"><span data-stu-id="40c9c-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="40c9c-161">Par défaut, EF Core effectue une requête à partir de la vue et envoie des mises à jour à la table.</span><span class="sxs-lookup"><span data-stu-id="40c9c-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="40c9c-162">Par exemple, l’exécution du code suivant :</span><span class="sxs-lookup"><span data-stu-id="40c9c-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="40c9c-163">Génère une requête sur la vue, puis une mise à jour de la table :</span><span class="sxs-lookup"><span data-stu-id="40c9c-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="40c9c-164">Configuration de la requête Split au niveau du contexte</span><span class="sxs-lookup"><span data-stu-id="40c9c-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="40c9c-165">Les requêtes de fractionnement (voir ci-dessous) peuvent désormais être configurées par défaut pour toute requête exécutée par DbContext.</span><span class="sxs-lookup"><span data-stu-id="40c9c-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="40c9c-166">Cette configuration n’est disponible que pour les fournisseurs relationnels et doit donc être spécifiée dans le cadre de la `UseProvider` Configuration.</span><span class="sxs-lookup"><span data-stu-id="40c9c-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="40c9c-167">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="40c9c-168">La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="40c9c-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="40c9c-169">Mappage PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="40c9c-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="40c9c-170">Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="40c9c-171">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="40c9c-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="40c9c-172">La classe .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native.</span><span class="sxs-lookup"><span data-stu-id="40c9c-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="40c9c-173">Pour plus d’informations, consultez les exemples `IPAddress` ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="40c9c-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="40c9c-174">Version préliminaire 7</span><span class="sxs-lookup"><span data-stu-id="40c9c-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="40c9c-175">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="40c9c-175">DbContextFactory</span></span>

<span data-ttu-id="40c9c-176">EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application.</span><span class="sxs-lookup"><span data-stu-id="40c9c-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="40c9c-177">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="40c9c-178">Les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite dépendre `IDbContextFactory<TContext>` du constructeur de service.</span><span class="sxs-lookup"><span data-stu-id="40c9c-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="40c9c-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-179">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="40c9c-180">Les instances de DbContext peuvent ensuite être créées et utilisées en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="40c9c-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="40c9c-181">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="40c9c-182">Notez que les instances de DbContext créées de cette façon _ne sont pas_ gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application.</span><span class="sxs-lookup"><span data-stu-id="40c9c-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="40c9c-183">Ce découplage est très utile pour les applications éblouissantes, où l’utilisation de `IDbContextFactory` est recommandée, mais peut également être utile dans d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="40c9c-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="40c9c-184">Les instances de DbContext peuvent être regroupées en appelant `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="40c9c-185">Ce regroupement fonctionne de la même façon que pour `AddDbContextPool` et présente également les mêmes limitations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="40c9c-186">La documentation est suivie d’un problème [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="40c9c-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="40c9c-187">Réinitialiser l’état de DbContext</span><span class="sxs-lookup"><span data-stu-id="40c9c-187">Reset DbContext state</span></span>

<span data-ttu-id="40c9c-188">EF Core 5,0 introduit `ChangeTracker.Clear()` , qui efface le DbContext de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="40c9c-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="40c9c-189">Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail.</span><span class="sxs-lookup"><span data-stu-id="40c9c-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="40c9c-190">Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus performante et fiable que le détachement en masse de toutes les entités.</span><span class="sxs-lookup"><span data-stu-id="40c9c-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="40c9c-191">La documentation est suivie d’un problème [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="40c9c-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="40c9c-192">Nouveau modèle pour les valeurs par défaut générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="40c9c-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="40c9c-193">EF Core permet de définir une valeur explicite pour une colonne qui peut également avoir une contrainte de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="40c9c-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="40c9c-194">EF Core utilise la valeur CLR par défaut de type de propriété comme sentinelle pour ce. Si la valeur n’est pas la valeur CLR par défaut, elle est insérée, sinon la valeur par défaut de la base de données est utilisée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="40c9c-195">Cela crée des problèmes pour les types où la valeur CLR par défaut n’est pas une bonne sentinelle, notamment les `bool` Propriétés.</span><span class="sxs-lookup"><span data-stu-id="40c9c-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="40c9c-196">EF Core 5,0 autorise désormais la valeur null dans le champ de stockage pour les cas comme celui-ci.</span><span class="sxs-lookup"><span data-stu-id="40c9c-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="40c9c-197">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-197">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="40c9c-198">Notez que le champ de stockage accepte la valeur null, mais pas la propriété exposée publiquement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="40c9c-199">Cela permet à la valeur de sentinelle de ne pas avoir `null` d’impact sur la surface publique du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="40c9c-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="40c9c-200">Dans ce cas, si le `IsValid` n’est jamais défini, la valeur par défaut de la base de données est utilisée, car le champ de stockage conserve la valeur null.</span><span class="sxs-lookup"><span data-stu-id="40c9c-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="40c9c-201">Si `true` ou `false` sont définis, cette valeur est enregistrée explicitement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="40c9c-202">La documentation est suivie d’un problème [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="40c9c-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="40c9c-203">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="40c9c-203">Cosmos partition keys</span></span>

<span data-ttu-id="40c9c-204">EF Core permet d’inclure la clé de partition Cosmos dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="40c9c-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="40c9c-205">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="40c9c-206">À partir de Preview 7, la clé de partition est incluse dans le PK du type d’entité et est utilisée pour améliorer les performances dans certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="40c9c-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="40c9c-207">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="40c9c-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="40c9c-208">Configuration de Cosmos</span><span class="sxs-lookup"><span data-stu-id="40c9c-208">Cosmos configuration</span></span>

<span data-ttu-id="40c9c-209">EF Core 5,0 améliore la configuration des connexions Cosmos et Cosmos.</span><span class="sxs-lookup"><span data-stu-id="40c9c-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="40c9c-210">Auparavant, EF Core nécessitait la spécification du point de terminaison et de la clé explicitement lors de la connexion à une base de données Cosmos.</span><span class="sxs-lookup"><span data-stu-id="40c9c-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="40c9c-211">EF Core 5,0 autorise l’utilisation d’une chaîne de connexion à la place.</span><span class="sxs-lookup"><span data-stu-id="40c9c-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="40c9c-212">En outre, EF Core 5,0 permet de définir explicitement l’instance WebProxy.</span><span class="sxs-lookup"><span data-stu-id="40c9c-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="40c9c-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="40c9c-214">De nombreuses autres valeurs de délai d’attente, limites, etc. peuvent également être configurées.</span><span class="sxs-lookup"><span data-stu-id="40c9c-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="40c9c-215">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-215">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="40c9c-216">Enfin, le mode de connexion par défaut est désormais `ConnectionMode.Gateway` , ce qui est généralement plus compatible.</span><span class="sxs-lookup"><span data-stu-id="40c9c-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="40c9c-217">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="40c9c-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="40c9c-218">Génération de modèles automatique-DbContext Now singularise</span><span class="sxs-lookup"><span data-stu-id="40c9c-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="40c9c-219">Auparavant, lors de la génération de modèles automatique d’une base de données existante, EF Core créera des noms de types d’entité qui correspondent aux noms de table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="40c9c-220">Par exemple, les tables `People` et ont `Addresses` abouti à des types d’entités nommés `People` et `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="40c9c-221">Dans les versions précédentes, ce comportement était configurable par l’inscription d’un service de pluralisation.</span><span class="sxs-lookup"><span data-stu-id="40c9c-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="40c9c-222">Désormais, dans EF Core 5,0, le package [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) est utilisé comme un service de pluralisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="40c9c-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="40c9c-223">Cela signifie que `People` les tables et `Addresses` sont maintenant rétroconçues dans les types d’entités nommés `Person` et `Address` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="40c9c-224">Points</span><span class="sxs-lookup"><span data-stu-id="40c9c-224">Savepoints</span></span>

<span data-ttu-id="40c9c-225">EF Core prend désormais en charge les [points](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) de contrôle pour un plus grand contrôle sur les transactions qui exécutent plusieurs opérations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-225">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="40c9c-226">Les points de sauvegarde peuvent être créés, libérés et restaurés manuellement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="40c9c-227">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="40c9c-228">En outre, les EF Core sont désormais restaurés jusqu’au dernier point de sauvegarde en cas d’échec de l’exécution `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="40c9c-229">Cela permet de faire une nouvelle tentative d’SaveChanges sans retenter l’intégralité de la transaction.</span><span class="sxs-lookup"><span data-stu-id="40c9c-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="40c9c-230">La documentation est suivie d’un problème [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="40c9c-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="40c9c-231">Préversion 6</span><span class="sxs-lookup"><span data-stu-id="40c9c-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="40c9c-232">Fractionner les requêtes pour les regroupements associés</span><span class="sxs-lookup"><span data-stu-id="40c9c-232">Split queries for related collections</span></span>

<span data-ttu-id="40c9c-233">À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="40c9c-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="40c9c-234">Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="40c9c-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="40c9c-235">Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.</span><span class="sxs-lookup"><span data-stu-id="40c9c-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="40c9c-236">EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="40c9c-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="40c9c-237">Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes.</span><span class="sxs-lookup"><span data-stu-id="40c9c-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="40c9c-238">Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.</span><span class="sxs-lookup"><span data-stu-id="40c9c-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="40c9c-239">Fractionner des requêtes avec include</span><span class="sxs-lookup"><span data-stu-id="40c9c-239">Split queries with Include</span></span>

<span data-ttu-id="40c9c-240">Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :</span><span class="sxs-lookup"><span data-stu-id="40c9c-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="40c9c-241">Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="40c9c-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="40c9c-242">La nouvelle `AsSplitQuery` API peut être utilisée pour modifier ce comportement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="40c9c-243">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="40c9c-244">AsSplitQuery est disponible pour tous les fournisseurs de bases de données relationnelles et peut être utilisée n’importe où dans la requête, tout comme AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="40c9c-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="40c9c-245">EF Core générera maintenant les trois requêtes SQL suivantes :</span><span class="sxs-lookup"><span data-stu-id="40c9c-245">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="40c9c-246">Toutes les opérations sur la racine de la requête sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="40c9c-246">All operations on the query root are supported.</span></span> <span data-ttu-id="40c9c-247">Cela comprend les opérations OrderBy/Skip/Take, Join, FirstOrDefault et des résultats uniques de sélection.</span><span class="sxs-lookup"><span data-stu-id="40c9c-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="40c9c-248">Notez que les inclusions filtrées avec OrderBy/Skip/Take ne sont pas prises en charge dans Preview 6, mais sont disponibles dans les builds quotidiennes et seront incluses dans Preview 7.</span><span class="sxs-lookup"><span data-stu-id="40c9c-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="40c9c-249">Fractionner des requêtes avec des projections de collection</span><span class="sxs-lookup"><span data-stu-id="40c9c-249">Split queries with collection projections</span></span>

<span data-ttu-id="40c9c-250">`AsSplitQuery` peut également être utilisé lorsque les collections sont chargées dans des projections.</span><span class="sxs-lookup"><span data-stu-id="40c9c-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="40c9c-251">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="40c9c-252">Cette requête LINQ génère les deux requêtes SQL suivantes lors de l’utilisation du fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="40c9c-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="40c9c-253">Notez que seule la matérialisation de la collection est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="40c9c-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="40c9c-254">Les compositions ultérieures `e.Albums` dans le cas ci-dessus n’aboutissent pas à une requête Split.</span><span class="sxs-lookup"><span data-stu-id="40c9c-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="40c9c-255">Les améliorations apportées à cette zone sont suivies par [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="40c9c-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="40c9c-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="40c9c-256">IndexAttribute</span></span>

<span data-ttu-id="40c9c-257">Le nouveau IndexAttribute peut être placé sur un type d’entité pour spécifier un index pour une colonne unique.</span><span class="sxs-lookup"><span data-stu-id="40c9c-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="40c9c-258">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="40c9c-259">Par SQL Server, les migrations génèrent alors le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="40c9c-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="40c9c-260">IndexAttribute peut également être utilisé pour spécifier un index couvrant plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="40c9c-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="40c9c-261">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-261">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="40c9c-262">Pour SQL Server, les résultats sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="40c9c-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="40c9c-263">La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="40c9c-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="40c9c-264">Amélioration des exceptions de traduction des requêtes</span><span class="sxs-lookup"><span data-stu-id="40c9c-264">Improved query translation exceptions</span></span>

<span data-ttu-id="40c9c-265">Nous continuons d’améliorer les messages d’exception générés en cas d’échec de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="40c9c-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="40c9c-266">Par exemple, cette requête utilise la propriété non mappée `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="40c9c-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="40c9c-267">EF Core lèvera l’exception suivante indiquant que la conversion a échoué, car `IsSigned` n’est pas mappé :</span><span class="sxs-lookup"><span data-stu-id="40c9c-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="40c9c-268">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-268">Unhandled exception.</span></span> <span data-ttu-id="40c9c-269">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. IsSigned) 'n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="40c9c-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="40c9c-270">Informations supplémentaires : la traduction du membre « IsSigned » sur le type d’entité « Artist » a échoué.</span><span class="sxs-lookup"><span data-stu-id="40c9c-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="40c9c-271">Éventuellement, le membre spécifié n’est pas mappé.</span><span class="sxs-lookup"><span data-stu-id="40c9c-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="40c9c-272">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="40c9c-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="40c9c-273">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="40c9c-274">De même, de meilleurs messages d’exception sont désormais générés lorsque vous tentez de traduire des comparaisons de chaînes avec la sémantique dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="40c9c-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="40c9c-275">Par exemple, cette requête tente d’utiliser `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="40c9c-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="40c9c-276">EF Core lèvera à présent l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="40c9c-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="40c9c-277">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-277">Unhandled exception.</span></span> <span data-ttu-id="40c9c-278">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. Name. Equals (valeur : « les licornes », comparisonType : CurrentCulture))» n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="40c9c-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="40c9c-279">Informations supplémentaires : traduction de « String ». La méthode Equals qui accepte l’argument’StringComparison’n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="40c9c-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="40c9c-280">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2129535 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="40c9c-281">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="40c9c-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="40c9c-282">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="40c9c-283">Spécifier l’ID de la transaction</span><span class="sxs-lookup"><span data-stu-id="40c9c-283">Specify transaction ID</span></span>

<span data-ttu-id="40c9c-284">Cette fonctionnalité a été ajoutée par la communauté [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="40c9c-285">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="40c9c-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="40c9c-286">EF Core expose un ID de transaction pour la corrélation des transactions entre les appels.</span><span class="sxs-lookup"><span data-stu-id="40c9c-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="40c9c-287">Cet ID est généralement défini par EF Core lorsqu’une transaction est démarrée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="40c9c-288">Si l’application démarre plutôt la transaction, cette fonctionnalité permet à l’application de définir explicitement l’ID de transaction afin qu’elle soit correctement corrélée partout où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="40c9c-289">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="40c9c-290">Mappage IPAddress</span><span class="sxs-lookup"><span data-stu-id="40c9c-290">IPAddress mapping</span></span>

<span data-ttu-id="40c9c-291">Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="40c9c-292">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="40c9c-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="40c9c-293">La classe .NET [IPAddress](/dotnet/api/system.net.ipaddress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native.</span><span class="sxs-lookup"><span data-stu-id="40c9c-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="40c9c-294">Par exemple, envisagez de mapper ce type d’entité :</span><span class="sxs-lookup"><span data-stu-id="40c9c-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="40c9c-295">Sur SQL Server, les migrations créent le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="40c9c-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="40c9c-296">Les entités peuvent ensuite être ajoutées de manière normale :</span><span class="sxs-lookup"><span data-stu-id="40c9c-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="40c9c-297">Et le SQL qui en résulte insère l’adresse IPv4 ou IPv6 normalisée :</span><span class="sxs-lookup"><span data-stu-id="40c9c-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="40c9c-298">Exclure OnConfiguring lors de la génération de modèles automatique</span><span class="sxs-lookup"><span data-stu-id="40c9c-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="40c9c-299">Lorsqu’un DbContext est généré à partir d’une base de données existante, EF Core par défaut crée une surcharge OnConfiguring avec une chaîne de connexion afin que le contexte soit immédiatement utilisable.</span><span class="sxs-lookup"><span data-stu-id="40c9c-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="40c9c-300">Toutefois, cela n’est pas utile si vous avez déjà une classe partielle avec OnConfiguring, ou si vous configurez le contexte d’une autre façon.</span><span class="sxs-lookup"><span data-stu-id="40c9c-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="40c9c-301">Pour résoudre ce risque, les commandes de génération de modèles automatique peuvent maintenant être demandées d’omettre la génération de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="40c9c-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="40c9c-302">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="40c9c-303">Ou dans la console du gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="40c9c-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="40c9c-304">Notez que nous vous recommandons [d’utiliser une chaîne de connexion nommée et un stockage sécurisé comme des secrets d’utilisateur](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="40c9c-304">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="40c9c-305">Traductions pour FirstOrDefault sur les chaînes</span><span class="sxs-lookup"><span data-stu-id="40c9c-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="40c9c-306">Cette fonctionnalité a été ajoutée par la communauté [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="40c9c-307">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="40c9c-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="40c9c-308">Les opérateurs FirstOrDefault et similaires pour les caractères dans les chaînes sont maintenant traduits.</span><span class="sxs-lookup"><span data-stu-id="40c9c-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="40c9c-309">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="40c9c-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="40c9c-310">Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :</span><span class="sxs-lookup"><span data-stu-id="40c9c-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="40c9c-311">Simplifier les blocs de casse</span><span class="sxs-lookup"><span data-stu-id="40c9c-311">Simplify case blocks</span></span>

<span data-ttu-id="40c9c-312">EF Core génère désormais de meilleures requêtes avec des blocs de cas.</span><span class="sxs-lookup"><span data-stu-id="40c9c-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="40c9c-313">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="40c9c-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="40c9c-314">Était sur SQL Server traduite formellement en :</span><span class="sxs-lookup"><span data-stu-id="40c9c-314">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="40c9c-315">Mais est maintenant traduit par :</span><span class="sxs-lookup"><span data-stu-id="40c9c-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="40c9c-316">Préversion 5</span><span class="sxs-lookup"><span data-stu-id="40c9c-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="40c9c-317">Classements de base de données</span><span class="sxs-lookup"><span data-stu-id="40c9c-317">Database collations</span></span>

<span data-ttu-id="40c9c-318">Le classement par défaut d’une base de données peut désormais être spécifié dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="40c9c-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="40c9c-319">Cela passera à des migrations générées pour définir le classement lors de la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="40c9c-320">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="40c9c-321">Les migrations génèrent ensuite les éléments suivants pour créer la base de données sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="40c9c-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="40c9c-322">Le classement à utiliser pour des colonnes de base de données spécifiques peut également être spécifié.</span><span class="sxs-lookup"><span data-stu-id="40c9c-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="40c9c-323">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="40c9c-324">Pour ceux qui n’utilisent pas de migrations, les classements sont à présent rétroconçus de la base de données lors de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="40c9c-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="40c9c-325">Enfin, le `EF.Functions.Collate()` autorise les requêtes ad hoc utilisant des classements différents.</span><span class="sxs-lookup"><span data-stu-id="40c9c-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="40c9c-326">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="40c9c-327">La requête suivante est générée pour SQL Server :</span><span class="sxs-lookup"><span data-stu-id="40c9c-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="40c9c-328">Notez que les classements ad hoc doivent être utilisés avec précaution, car ils peuvent avoir un impact négatif sur les performances de la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="40c9c-329">La documentation est suivie d’un problème [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="40c9c-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="40c9c-330">Transmettre les arguments dans IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="40c9c-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="40c9c-331">Les arguments sont à présent transmis à partir de la ligne de commande dans la `CreateDbContext` méthode de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="40c9c-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="40c9c-332">Par exemple, pour indiquer qu’il s’agit d’une build dev, un argument personnalisé (par exemple, `dev` ) peut être passé sur la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="40c9c-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="40c9c-333">Cet argument est ensuite transmis à la fabrique, où il peut être utilisé pour contrôler la façon dont le contexte est créé et initialisé.</span><span class="sxs-lookup"><span data-stu-id="40c9c-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="40c9c-334">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="40c9c-335">La documentation est suivie d’un problème [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="40c9c-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="40c9c-336">Requêtes de suivi sans suivi de la résolution d’identité</span><span class="sxs-lookup"><span data-stu-id="40c9c-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="40c9c-337">Les requêtes de non-suivi peuvent maintenant être configurées pour effectuer la résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="40c9c-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="40c9c-338">Par exemple, la requête suivante crée une nouvelle instance de blog pour chaque publication, même si chaque blog a la même clé primaire.</span><span class="sxs-lookup"><span data-stu-id="40c9c-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="40c9c-339">Toutefois, au détriment d’un peu plus lentement et en utilisant toujours plus de mémoire, cette requête peut être modifiée pour garantir qu’une seule instance de blog est créée :</span><span class="sxs-lookup"><span data-stu-id="40c9c-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="40c9c-340">Notez que cela n’est utile que pour les requêtes sans suivi, car toutes les requêtes de suivi présentent déjà ce comportement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="40c9c-341">En outre, la syntaxe de l’API suivante `PerformIdentityResolution` sera modifiée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="40c9c-342">Consultez [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="40c9c-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="40c9c-343">La documentation est suivie d’un problème [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="40c9c-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="40c9c-344">Colonnes calculées (persistantes) stockées</span><span class="sxs-lookup"><span data-stu-id="40c9c-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="40c9c-345">La plupart des bases de données autorisent le stockage des valeurs de colonne calculées après le calcul.</span><span class="sxs-lookup"><span data-stu-id="40c9c-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="40c9c-346">Bien que cela prenne de l’espace disque, la colonne calculée n’est calculée qu’une seule fois lors de la mise à jour, et non chaque fois que sa valeur est récupérée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="40c9c-347">Cela permet également d’indexer la colonne pour certaines bases de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="40c9c-348">EF Core 5,0 permet de configurer les colonnes calculées comme stockées.</span><span class="sxs-lookup"><span data-stu-id="40c9c-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="40c9c-349">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="40c9c-350">SQLite colonnes calculées</span><span class="sxs-lookup"><span data-stu-id="40c9c-350">SQLite computed columns</span></span>

<span data-ttu-id="40c9c-351">EF Core prend désormais en charge les colonnes calculées dans les bases de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="40c9c-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="40c9c-352">Preview 4</span><span class="sxs-lookup"><span data-stu-id="40c9c-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="40c9c-353">Configurer la précision/l’échelle de la base de données dans le modèle</span><span class="sxs-lookup"><span data-stu-id="40c9c-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="40c9c-354">La précision et l’échelle d’une propriété peuvent désormais être spécifiées à l’aide du générateur de modèles.</span><span class="sxs-lookup"><span data-stu-id="40c9c-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="40c9c-355">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="40c9c-356">La précision et l’échelle peuvent toujours être définies par le biais du type de base de données complet, par exemple « Decimal (16, 4) ».</span><span class="sxs-lookup"><span data-stu-id="40c9c-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="40c9c-357">La documentation est suivie d’un problème [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="40c9c-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="40c9c-358">Spécifier SQL Server facteur de remplissage d’index</span><span class="sxs-lookup"><span data-stu-id="40c9c-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="40c9c-359">Le facteur de remplissage peut désormais être spécifié lors de la création d’un index sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="40c9c-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="40c9c-360">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="40c9c-361">Preview 3</span><span class="sxs-lookup"><span data-stu-id="40c9c-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="40c9c-362">Include filtré</span><span class="sxs-lookup"><span data-stu-id="40c9c-362">Filtered Include</span></span>

<span data-ttu-id="40c9c-363">La méthode Include prend désormais en charge le filtrage des entités incluses.</span><span class="sxs-lookup"><span data-stu-id="40c9c-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="40c9c-364">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="40c9c-365">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="40c9c-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="40c9c-366">Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.</span><span class="sxs-lookup"><span data-stu-id="40c9c-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="40c9c-367">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="40c9c-368">Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="40c9c-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="40c9c-369">Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="40c9c-370">Nouvelle API ModelBuilder pour les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="40c9c-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="40c9c-371">Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="40c9c-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="40c9c-372">Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="40c9c-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="40c9c-373">Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :</span><span class="sxs-lookup"><span data-stu-id="40c9c-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="40c9c-374">Notez que l' `Navigation` API ne remplace pas la configuration de la relation.</span><span class="sxs-lookup"><span data-stu-id="40c9c-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="40c9c-375">Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.</span><span class="sxs-lookup"><span data-stu-id="40c9c-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="40c9c-376">Consultez la [documentation](xref:core/modeling/relationships#configuring-navigation-properties)sur la configuration des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="40c9c-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="40c9c-377">Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="40c9c-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="40c9c-378">Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="40c9c-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="40c9c-379">Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :</span><span class="sxs-lookup"><span data-stu-id="40c9c-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="40c9c-380">Pour plus d’informations, consultez la documentation sur les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et l' [ingénierie à rebours](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="40c9c-381">En outre, une chaîne de connexion peut désormais être passée à la `database-update` commande :</span><span class="sxs-lookup"><span data-stu-id="40c9c-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="40c9c-382">Pour plus d’informations, consultez la documentation sur les [Outils](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="40c9c-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="40c9c-383">Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.</span><span class="sxs-lookup"><span data-stu-id="40c9c-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="40c9c-384">EnableDetailedErrors a retourné</span><span class="sxs-lookup"><span data-stu-id="40c9c-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="40c9c-385">Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="40c9c-386">Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="40c9c-387">L’utilisation de `EnableDetailedErrors` permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.</span><span class="sxs-lookup"><span data-stu-id="40c9c-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="40c9c-388">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-388">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="40c9c-389">La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="40c9c-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="40c9c-390">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="40c9c-390">Cosmos partition keys</span></span>

<span data-ttu-id="40c9c-391">La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="40c9c-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="40c9c-392">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="40c9c-393">La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="40c9c-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="40c9c-394">Prise en charge de la fonction DATALENGTH SQL Server</span><span class="sxs-lookup"><span data-stu-id="40c9c-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="40c9c-395">Vous pouvez y accéder à l’aide de la nouvelle `EF.Functions.DataLength` méthode.</span><span class="sxs-lookup"><span data-stu-id="40c9c-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="40c9c-396">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-396">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="40c9c-397">Préversion 2</span><span class="sxs-lookup"><span data-stu-id="40c9c-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="40c9c-398">Utiliser un attribut C# pour spécifier un champ de stockage de propriété</span><span class="sxs-lookup"><span data-stu-id="40c9c-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="40c9c-399">Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="40c9c-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="40c9c-400">Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.</span><span class="sxs-lookup"><span data-stu-id="40c9c-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="40c9c-401">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-401">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="40c9c-402">La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="40c9c-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="40c9c-403">Terminer le mappage de discriminateur</span><span class="sxs-lookup"><span data-stu-id="40c9c-403">Complete discriminator mapping</span></span>

<span data-ttu-id="40c9c-404">EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="40c9c-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="40c9c-405">Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.</span><span class="sxs-lookup"><span data-stu-id="40c9c-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="40c9c-406">EF Core 5,0 implémente désormais ces améliorations.</span><span class="sxs-lookup"><span data-stu-id="40c9c-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="40c9c-407">Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="40c9c-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="40c9c-408">EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :</span><span class="sxs-lookup"><span data-stu-id="40c9c-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="40c9c-409">Il s’agit du comportement par défaut à partir de Preview 3.</span><span class="sxs-lookup"><span data-stu-id="40c9c-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="40c9c-410">Améliorations des performances dans Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="40c9c-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="40c9c-411">Nous avons apporté deux améliorations des performances pour SQLIte :</span><span class="sxs-lookup"><span data-stu-id="40c9c-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="40c9c-412">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="40c9c-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="40c9c-413">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="40c9c-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="40c9c-414">Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.</span><span class="sxs-lookup"><span data-stu-id="40c9c-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="40c9c-415">Preview 1</span><span class="sxs-lookup"><span data-stu-id="40c9c-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="40c9c-416">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="40c9c-416">Simple logging</span></span>

<span data-ttu-id="40c9c-417">Cette fonctionnalité ajoute des fonctionnalités similaires à `Database.Log` dans EF6.</span><span class="sxs-lookup"><span data-stu-id="40c9c-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="40c9c-418">Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.</span><span class="sxs-lookup"><span data-stu-id="40c9c-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="40c9c-419">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="40c9c-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="40c9c-420">Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.</span><span class="sxs-lookup"><span data-stu-id="40c9c-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="40c9c-421">Moyen simple d’utiliser le SQL généré</span><span class="sxs-lookup"><span data-stu-id="40c9c-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="40c9c-422">EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="40c9c-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="40c9c-423">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="40c9c-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="40c9c-424">Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.</span><span class="sxs-lookup"><span data-stu-id="40c9c-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="40c9c-425">Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé</span><span class="sxs-lookup"><span data-stu-id="40c9c-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="40c9c-426">Un type d’entité peut désormais être configuré comme n’ayant aucune clé à l’aide de la nouvelle `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="40c9c-427">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="40c9c-428">La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="40c9c-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="40c9c-429">La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé</span><span class="sxs-lookup"><span data-stu-id="40c9c-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="40c9c-430">Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="40c9c-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="40c9c-431">En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="40c9c-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="40c9c-432">Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.</span><span class="sxs-lookup"><span data-stu-id="40c9c-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="40c9c-433">La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="40c9c-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="40c9c-434">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="40c9c-434">Change-tracking proxies</span></span>

<span data-ttu-id="40c9c-435">EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) et [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="40c9c-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="40c9c-436">Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.</span><span class="sxs-lookup"><span data-stu-id="40c9c-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="40c9c-437">Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.</span><span class="sxs-lookup"><span data-stu-id="40c9c-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="40c9c-438">La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="40c9c-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="40c9c-439">Vues de débogage améliorées</span><span class="sxs-lookup"><span data-stu-id="40c9c-439">Enhanced debug views</span></span>

<span data-ttu-id="40c9c-440">Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.</span><span class="sxs-lookup"><span data-stu-id="40c9c-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="40c9c-441">Une vue de débogage a été implémentée pour le modèle il y a quelque temps.</span><span class="sxs-lookup"><span data-stu-id="40c9c-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="40c9c-442">Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="40c9c-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="40c9c-443">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="40c9c-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="40c9c-444">Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.</span><span class="sxs-lookup"><span data-stu-id="40c9c-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="40c9c-445">Amélioration de la gestion de la sémantique null de la base de données</span><span class="sxs-lookup"><span data-stu-id="40c9c-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="40c9c-446">En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.</span><span class="sxs-lookup"><span data-stu-id="40c9c-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="40c9c-447">Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.</span><span class="sxs-lookup"><span data-stu-id="40c9c-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="40c9c-448">EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.</span><span class="sxs-lookup"><span data-stu-id="40c9c-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="40c9c-449">EF Core 5,0 améliore considérablement l’efficacité de ces traductions.</span><span class="sxs-lookup"><span data-stu-id="40c9c-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="40c9c-450">La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="40c9c-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="40c9c-451">Propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="40c9c-451">Indexer properties</span></span>

<span data-ttu-id="40c9c-452">EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.</span><span class="sxs-lookup"><span data-stu-id="40c9c-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="40c9c-453">Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="40c9c-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="40c9c-454">La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="40c9c-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="40c9c-455">Génération de contraintes de validation pour les mappages d’énumération</span><span class="sxs-lookup"><span data-stu-id="40c9c-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="40c9c-456">EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.</span><span class="sxs-lookup"><span data-stu-id="40c9c-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="40c9c-457">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="40c9c-458">La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="40c9c-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="40c9c-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="40c9c-459">IsRelational</span></span>

<span data-ttu-id="40c9c-460">Une nouvelle `IsRelational` méthode a été ajoutée en plus des `IsSqlServer` , `IsSqlite` , et existants `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="40c9c-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="40c9c-461">Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="40c9c-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="40c9c-462">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="40c9c-463">La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="40c9c-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="40c9c-464">Accès concurrentiel optimiste Cosmos avec ETags</span><span class="sxs-lookup"><span data-stu-id="40c9c-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="40c9c-465">Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.</span><span class="sxs-lookup"><span data-stu-id="40c9c-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="40c9c-466">Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :</span><span class="sxs-lookup"><span data-stu-id="40c9c-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="40c9c-467">SaveChanges lèvera ensuite un en cas de `DbUpdateConcurrencyException` conflit d’accès concurrentiel, qui [peut être géré](/ef/core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="40c9c-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="40c9c-468">La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="40c9c-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="40c9c-469">Traductions de requêtes pour d’autres constructions DateTime</span><span class="sxs-lookup"><span data-stu-id="40c9c-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="40c9c-470">Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.</span><span class="sxs-lookup"><span data-stu-id="40c9c-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="40c9c-471">En outre, les fonctions de SQL Server suivantes sont maintenant mappées :</span><span class="sxs-lookup"><span data-stu-id="40c9c-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="40c9c-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="40c9c-472">DateDiffWeek</span></span>
* <span data-ttu-id="40c9c-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="40c9c-473">DateFromParts</span></span>

<span data-ttu-id="40c9c-474">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="40c9c-475">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="40c9c-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="40c9c-476">Traductions de requêtes pour d’autres constructions de tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="40c9c-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="40c9c-477">Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="40c9c-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="40c9c-478">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="40c9c-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="40c9c-479">Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.</span><span class="sxs-lookup"><span data-stu-id="40c9c-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="40c9c-480">Traduction de requête pour l’inverse</span><span class="sxs-lookup"><span data-stu-id="40c9c-480">Query translation for Reverse</span></span>

<span data-ttu-id="40c9c-481">Les requêtes utilisant `Reverse` sont désormais traduites.</span><span class="sxs-lookup"><span data-stu-id="40c9c-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="40c9c-482">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="40c9c-483">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="40c9c-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="40c9c-484">Traduction des requêtes pour les opérateurs au niveau du bit</span><span class="sxs-lookup"><span data-stu-id="40c9c-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="40c9c-485">Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :</span><span class="sxs-lookup"><span data-stu-id="40c9c-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="40c9c-486">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="40c9c-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="40c9c-487">Traduction des requêtes pour les chaînes sur Cosmos</span><span class="sxs-lookup"><span data-stu-id="40c9c-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="40c9c-488">Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="40c9c-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="40c9c-489">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="40c9c-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
