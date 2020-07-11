---
title: Application de migrations-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238752"
---
# <a name="applying-migrations"></a>Application de migrations

Une fois vos migrations ajoutées, elles doivent être déployées et appliquées à vos bases de données. Il existe plusieurs stratégies pour y parvenir, dont certaines sont plus appropriées pour les environnements de production, et d’autres pour le cycle de vie du développement.

> [!NOTE]
> Quelle que soit votre stratégie de déploiement, Inspectez toujours les migrations générées et testez-les avant de les appliquer à une base de données de production. Une migration peut supprimer une colonne lorsque l’intention est de la renommer ou peut échouer pour différentes raisons lorsqu’elle est appliquée à une base de données.

## <a name="sql-scripts"></a>Scripts SQL

La méthode recommandée pour déployer des migrations vers une base de données de production consiste à générer des scripts SQL. Les avantages de cette stratégie sont les suivants :

* La précision des scripts SQL peut être vérifiée ; Cela est important, car l’application de modifications de schéma aux bases de données de production est une opération potentiellement dangereuse qui peut impliquer une perte de données.
* Dans certains cas, les scripts peuvent être réglés pour répondre aux besoins spécifiques d’une base de données de production.
* Les scripts SQL peuvent être utilisés conjointement avec une technologie de déploiement et peuvent même être générés dans le cadre de votre processus d’intégration continue.
* Les scripts SQL peuvent être fournis à un administrateur de bases de donnée et peuvent être gérés et archivés séparément.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Utilisation de base

L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Avec From (To implicite)

L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>Avec From et To

L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration.

> [!WARNING]
> Prenez note des scénarios de pertes de données potentielles.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Utilisation de base

L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Avec From (To implicite)

L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>Avec From et To

L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.

```powershell
Script-Migration AddNewTables AddAuditTable
```
Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration. *Prenez note des scénarios de pertes de données potentielles.*

***

La génération de script accepte les deux arguments suivants dans, indiquez la plage de migrations à générer :

* La migration **from** doit être la dernière migration appliquée à la base de données avant l’exécution du script. Si aucune migration n’a été appliquée, spécifiez `0` (il s’agit de la valeur par défaut).
* La migration **to** est la dernière migration à appliquer à la base de données après l’exécution du script. Par défaut, il s’agit de la dernière migration dans votre projet.

## <a name="idempotent-sql-scripts"></a>Scripts SQL idempotent

Les scripts SQL générés ci-dessus ne peuvent être appliqués que pour modifier votre schéma d’une migration vers une autre. il vous incombe d’appliquer le script de manière appropriée et uniquement à la base de données dans l’état de migration approprié. EF Core prend également en charge la génération de scripts **idempotent** , qui vérifient en interne les migrations qui ont déjà été appliquées (par le biais de la table d’historique des migrations) et ne s’appliquent qu’à celles qui sont manquantes. Cela est utile si vous ne savez pas exactement quelle est la dernière migration appliquée à la base de données, ou si vous déployez sur plusieurs bases de données qui peuvent chacune être dans une migration différente.

La commande suivante génère des migrations idempotent :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>Outils en ligne de commande

Les outils en ligne de commande EF peuvent être utilisés pour appliquer des migrations à une base de données. Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas idéale pour la gestion des bases de données de production :

* Les commandes SQL sont appliquées directement par l’outil, sans donner au développeur une modification pour les inspecter ou les modifier. Cela peut être dangereux dans un environnement de production.
* Le kit de développement logiciel (SDK) .NET et l’outil EF doivent être installés sur les serveurs de production.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

Le code suivant met à jour votre base de données avec la dernière migration :

```dotnetcli
dotnet ef database update
```

Le code suivant met à jour votre base de données vers une migration donnée :

```dotnetcli
dotnet ef database update AddNewTables
```

Notez que cela peut être utilisé pour revenir à une migration antérieure également.

> [!WARNING]
> Prenez note des scénarios de pertes de données potentielles.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Le code suivant met à jour votre base de données avec la dernière migration :

``` powershell
Update-Database
```

Le code suivant met à jour votre base de données vers une migration donnée :

``` powershell
Update-Database AddNewTables
```

Notez que cela peut être utilisé pour revenir à une migration antérieure également.

> [!WARNING]
> Prenez note des scénarios de pertes de données potentielles.

***

Pour plus d’informations sur l’application des migrations à l’aide des outils en ligne de commande, consultez la référence sur les [outils de EF Core](xref:core/miscellaneous/cli/index).

## <a name="apply-migrations-at-runtime"></a>Appliquer des migrations au moment de l’exécution

Il est possible que l’application elle-même applique des migrations par programme, en général au démarrage. Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas appropriée pour la gestion des bases de données de production, pour les raisons suivantes :

* Si plusieurs instances de votre application sont en cours d’exécution, les deux applications peuvent tenter d’appliquer la migration simultanément et échouer (ou pire, entraîner une altération des données).
* De même, si une application accède à la base de données alors qu’une autre application la migre, cela peut entraîner de graves problèmes.
* L’application doit disposer d’un accès avec élévation de privilèges pour modifier le schéma de la base de données. Il est généralement conseillé de limiter les autorisations de base de données de l’application en production.
* Il est important de pouvoir restaurer une migration appliquée en cas de problème. Les autres stratégies le fournissent facilement et en toute simplicité.
* Les commandes SQL sont appliquées directement par le programme, sans donner au développeur une modification pour les inspecter ou les modifier. Cela peut être dangereux dans un environnement de production.

Pour appliquer des migrations par programme, appelez `context.Database.Migrate()` . Par exemple, une application ASP.NET classique peut effectuer les opérations suivantes :

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

Notez que s' `Migrate()` appuie sur le `IMigrator` service, qui peut être utilisé pour des scénarios plus avancés. Utilisez `myDbContext.GetInfrastructure().GetService<IMigrator>()` pour y accéder.

> [!WARNING]
>
> * Réfléchissez soigneusement avant d’utiliser cette approche en production. L’expérience a montré que la simplicité de cette stratégie de déploiement est dépensée par les problèmes qu’elle crée. Envisagez plutôt d’utiliser des scripts SQL.
> * N’appelez pas `EnsureCreated()` avant `Migrate()`. `EnsureCreated()` ignore Migrations pour créer le schéma, ce qui entraîne l’échec de `Migrate()`.
