---
title: Opérations sur les migrations personnalisées-EF Core
description: Gestion des migrations SQL personnalisées et brutes pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 708894d8d567a4644be3a4ace98cc837465710e0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617948"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="46bbe-103">Opérations de migration personnalisées</span><span class="sxs-lookup"><span data-stu-id="46bbe-103">Custom Migrations Operations</span></span>

<span data-ttu-id="46bbe-104">L’API MigrationBuilder vous permet d’effectuer de nombreux types d’opérations différents au cours d’une migration, mais elle est loin d’être exhaustive.</span><span class="sxs-lookup"><span data-stu-id="46bbe-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="46bbe-105">Toutefois, l’API est également extensible, ce qui vous permet de définir vos propres opérations.</span><span class="sxs-lookup"><span data-stu-id="46bbe-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="46bbe-106">Il existe deux façons d’étendre l’API : à l’aide de la `Sql()` méthode, ou en définissant des `MigrationOperation` objets personnalisés.</span><span class="sxs-lookup"><span data-stu-id="46bbe-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="46bbe-107">Pour illustrer cela, examinons l’implémentation d’une opération qui crée un utilisateur de base de données à l’aide de chaque approche.</span><span class="sxs-lookup"><span data-stu-id="46bbe-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="46bbe-108">Dans nos migrations, nous souhaitons activer l’écriture du code suivant :</span><span class="sxs-lookup"><span data-stu-id="46bbe-108">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="46bbe-109">Utilisation de MigrationBuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="46bbe-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="46bbe-110">Le moyen le plus simple d’implémenter une opération personnalisée consiste à définir une méthode d’extension qui appelle `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="46bbe-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="46bbe-111">Voici un exemple qui génère le Transact-SQL approprié.</span><span class="sxs-lookup"><span data-stu-id="46bbe-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="46bbe-112">Si vos migrations doivent prendre en charge plusieurs fournisseurs de bases de données, vous pouvez utiliser la `MigrationBuilder.ActiveProvider` propriété.</span><span class="sxs-lookup"><span data-stu-id="46bbe-112">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="46bbe-113">Voici un exemple qui prend en charge à la fois Microsoft SQL Server et PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="46bbe-113">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

<span data-ttu-id="46bbe-114">Cette approche fonctionne uniquement si vous connaissez tous les fournisseurs où votre opération personnalisée sera appliquée.</span><span class="sxs-lookup"><span data-stu-id="46bbe-114">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="46bbe-115">Utilisation d’un MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="46bbe-115">Using a MigrationOperation</span></span>

<span data-ttu-id="46bbe-116">Pour découpler l’opération personnalisée de SQL, vous pouvez définir votre propre opération `MigrationOperation` pour la représenter.</span><span class="sxs-lookup"><span data-stu-id="46bbe-116">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="46bbe-117">L’opération est ensuite transmise au fournisseur afin de pouvoir déterminer le SQL approprié à générer.</span><span class="sxs-lookup"><span data-stu-id="46bbe-117">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="46bbe-118">Avec cette approche, la méthode d’extension doit simplement ajouter l’une de ces opérations à `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="46bbe-118">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

<span data-ttu-id="46bbe-119">Cette approche nécessite que chaque fournisseur sache comment générer SQL pour cette opération dans son `IMigrationsSqlGenerator` service.</span><span class="sxs-lookup"><span data-stu-id="46bbe-119">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="46bbe-120">Voici un exemple qui remplace le générateur de SQL Server pour gérer la nouvelle opération.</span><span class="sxs-lookup"><span data-stu-id="46bbe-120">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

<span data-ttu-id="46bbe-121">Remplacez le service SQL Generator des migrations par défaut par le service mis à jour.</span><span class="sxs-lookup"><span data-stu-id="46bbe-121">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
