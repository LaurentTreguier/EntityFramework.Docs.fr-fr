---
title: 'Définition de valeurs explicites pour les propriétés générées : EF Core'
description: Informations sur la définition explicite des valeurs pour les propriétés configurées comme générées avec Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: efaa87356a78c4ec7e11d57c1effad776bd01cba
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072484"
---
# <a name="setting-explicit-values-for-generated-properties"></a>Définition de valeurs explicites pour les propriétés générées

Une propriété générée est une propriété dont la valeur est générée (soit par la base de données soit par EF) lorsque l’entité est ajoutée ou mise à jour. Pour plus d’informations, consultez [Propriétés générées](xref:core/modeling/generated-properties).

Il peut arriver que vous souhaitiez définir une valeur explicite pour une propriété générée, au lieu d’en générer une.

> [!TIP]  
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/) sur GitHub.

## <a name="the-model"></a>Le modèle

Le modèle utilisé dans cet article contient une seule entité `Employee`.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>Enregistrement d’une valeur explicite lors de l’ajout

La propriété `Employee.EmploymentStarted` est configurée pour avoir des valeurs générées par la base de données pour les nouvelles entités (avec une valeur par défaut).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

Le code suivant insère deux employés dans la base de données.

* Pour le premier, aucune valeur n’est attribuée à la propriété `Employee.EmploymentStarted`, elle reste donc définie sur la valeur par défaut de CLR pour `DateTime`.
* Pour le deuxième, nous avons défini une valeur explicite de `1-Jan-2000`.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

La sortie indique que la base de données a généré une valeur pour le premier employé et utilisé notre valeur explicite pour le deuxième.

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>Valeurs explicites dans les colonnes IDENTITY de SQL Server

Par convention la propriété `Employee.EmployeeId` est une colonne `IDENTITY` générée par le magasin.

Pour la plupart des cas, l’approche illustrée ci-dessus fonctionne pour les propriétés de clé. Toutefois, pour insérer des valeurs explicites dans une colonne `IDENTITY` SQL Server, vous devez activer manuellement `IDENTITY_INSERT` avant d’appeler `SaveChanges()`.

> [!NOTE]  
> Nous avons une [demande de fonctionnalité](https://github.com/aspnet/EntityFramework/issues/703) dans notre backlog pour faire cela automatiquement dans le fournisseur SQL Server.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

La sortie indique que les ID fournis ont été enregistrés dans la base de données.

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>Définition d’une valeur explicite pendant une mise à jour

La propriété `Employee.LastPayRaise` est configurée pour avoir des valeurs générées par la base de données lors des mises à jour.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> Par défaut, EF Core lève une exception si vous essayez d’enregistrer une valeur explicite pour une propriété qui est configurée pour être générée pendant la mise à jour. Pour éviter ce problème, vous devez descendre au niveau de l’API de bas niveau et définir `AfterSaveBehavior` (comme indiqué ci-dessus).

> [!NOTE]  
> **Modifications dans EF Core 2.0 :** dans les versions précédentes, le comportement après enregistrement était contrôlé par le biais de l’indicateur `IsReadOnlyAfterSave`. Cet indicateur est obsolète et a été remplacé par `AfterSaveBehavior`.

Il existe également un déclencheur dans la base de données pour générer des valeurs pour la colonne `LastPayRaise` pendant les opérations `UPDATE`.

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

Le code suivant augmente le salaire des deux employés dans la base de données.

* Pour le premier, aucune valeur n’est attribuée à la propriété `Employee.LastPayRaise`, elle reste donc définie sur null.
* Pour le second, nous avons défini une valeur explicite d’une semaine plus tôt (rétroactivation de l’augmentation de salaire).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

La sortie indique que la base de données a généré une valeur pour le premier employé et utilisé notre valeur explicite pour le deuxième.

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
