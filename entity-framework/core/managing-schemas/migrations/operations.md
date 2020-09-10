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
# <a name="custom-migrations-operations"></a>Opérations de migration personnalisées

L’API MigrationBuilder vous permet d’effectuer de nombreux types d’opérations différents au cours d’une migration, mais elle est loin d’être exhaustive. Toutefois, l’API est également extensible, ce qui vous permet de définir vos propres opérations. Il existe deux façons d’étendre l’API : à l’aide de la `Sql()` méthode, ou en définissant des `MigrationOperation` objets personnalisés.

Pour illustrer cela, examinons l’implémentation d’une opération qui crée un utilisateur de base de données à l’aide de chaque approche. Dans nos migrations, nous souhaitons activer l’écriture du code suivant :

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Utilisation de MigrationBuilder. SQL ()

Le moyen le plus simple d’implémenter une opération personnalisée consiste à définir une méthode d’extension qui appelle `MigrationBuilder.Sql()` . Voici un exemple qui génère le Transact-SQL approprié.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

Si vos migrations doivent prendre en charge plusieurs fournisseurs de bases de données, vous pouvez utiliser la `MigrationBuilder.ActiveProvider` propriété. Voici un exemple qui prend en charge à la fois Microsoft SQL Server et PostgreSQL.

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

Cette approche fonctionne uniquement si vous connaissez tous les fournisseurs où votre opération personnalisée sera appliquée.

## <a name="using-a-migrationoperation"></a>Utilisation d’un MigrationOperation

Pour découpler l’opération personnalisée de SQL, vous pouvez définir votre propre opération `MigrationOperation` pour la représenter. L’opération est ensuite transmise au fournisseur afin de pouvoir déterminer le SQL approprié à générer.

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

Avec cette approche, la méthode d’extension doit simplement ajouter l’une de ces opérations à `MigrationBuilder.Operations` .

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

Cette approche nécessite que chaque fournisseur sache comment générer SQL pour cette opération dans son `IMigrationsSqlGenerator` service. Voici un exemple qui remplace le générateur de SQL Server pour gérer la nouvelle opération.

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

Remplacez le service SQL Generator des migrations par défaut par le service mis à jour.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
