---
title: Héritage-EF Core
description: Comment configurer l’héritage de type d’entité à l’aide de Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 11bd653a53767aa732790b1222da1beff8ad26a9
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635755"
---
# <a name="inheritance"></a>Héritage

EF peut mapper une hiérarchie de types .NET à une base de données. Cela vous permet d’écrire vos entités .NET dans le code comme d’habitude, à l’aide de types de base et dérivés, et de faire en sorte que EF crée en toute transparence le schéma de base de données approprié, les requêtes d’émission, etc. Les détails réels de la façon dont une hiérarchie de types est mappée dépendent du fournisseur ; Cette page décrit la prise en charge de l’héritage dans le contexte d’une base de données relationnelle.

## <a name="entity-type-hierarchy-mapping"></a>Mappage de la hiérarchie des types d’entités

Par Convention, EF ne recherche pas automatiquement les types de base ou dérivés ; Cela signifie que si vous souhaitez qu’un type CLR de votre hiérarchie soit mappé, vous devez spécifier explicitement ce type sur votre modèle. Par exemple, si vous spécifiez uniquement le type de base d’une hiérarchie, EF Core n’inclura pas tous ses sous-types de manière implicite.

L’exemple suivant expose un DbSet pour `Blog` et sa sous-classe `RssBlog` . Si `Blog` a une autre sous-classe, il ne sera pas inclus dans le modèle.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> Les colonnes de base de données deviennent automatiquement Nullable si nécessaire lors de l’utilisation du mappage TPH. Par exemple, la `RssUrl` colonne accepte les valeurs NULL, car les instances régulières `Blog` n’ont pas cette propriété.

Si vous ne souhaitez pas exposer un `DbSet` pour une ou plusieurs entités dans la hiérarchie, vous pouvez également utiliser l’API Fluent pour vous assurer qu’elles sont incluses dans le modèle.

> [!TIP]
> Si vous ne vous fiez pas aux conventions, vous pouvez spécifier explicitement le type de base à l’aide de `HasBaseType` . Vous pouvez également utiliser `.HasBaseType((Type)null)` pour supprimer un type d’entité de la hiérarchie.

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>Configuration TPH (table par hiérarchie) et discriminateur

Par défaut, EF mappe l’héritage à l’aide du modèle TPH ( *table par hiérarchie* ). TPH utilise une table unique pour stocker les données de tous les types dans la hiérarchie, et une colonne de discriminateur est utilisée pour identifier le type représenté par chaque ligne.

Le modèle ci-dessus est mappé au schéma de base de données suivant (Notez la colonne créée implicitement `Discriminator` , qui identifie le type de qui `Blog` est stocké dans chaque ligne).

![Capture d’écran des résultats de l’interrogation de la hiérarchie d’entité de blog à l’aide d’un modèle TPH (table par hiérarchie)](_static/inheritance-tph-data.png)

Vous pouvez configurer le nom et le type de la colonne de discriminateur et les valeurs utilisées pour identifier chaque type dans la hiérarchie :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

Dans les exemples ci-dessus, EF a ajouté le discriminateur implicitement en tant que [propriété Shadow](xref:core/modeling/shadow-properties) sur l’entité de base de la hiérarchie. Cette propriété peut être configurée comme n’importe quelle autre :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Enfin, le discriminateur peut également être mappé à une propriété .NET normale dans votre entité :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

Lors de l’interrogation d’entités dérivées, qui utilisent le modèle TPH, EF Core ajoute un prédicat sur la colonne de discriminateur dans la requête. Ce filtre permet de s’assurer que nous n’obtenons pas de lignes supplémentaires pour les types de base ou les types frères non dans le résultat. Ce prédicat de filtre est ignoré pour le type d’entité de base, car l’interrogation de l’entité de base obtient les résultats de toutes les entités de la hiérarchie. Lors de la matérialisation des résultats à partir d’une requête, si nous fournissons une valeur de discriminateur qui n’est pas mappée à un type d’entité dans le modèle, nous levez une exception, car nous ne savons pas comment matérialiser les résultats. Cette erreur se produit uniquement si votre base de données contient des lignes avec des valeurs de discriminateur qui ne sont pas mappées dans le modèle EF. Si vous disposez de ces données, vous pouvez marquer le mappage du discriminateur dans EF Core modèle comme incomplet pour indiquer que nous devrions toujours ajouter un prédicat de filtre pour interroger un type dans la hiérarchie. `IsComplete(false)` l’appel sur la configuration de discriminateur marque le mappage comme étant incomplet.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a>Colonnes partagées

Par défaut, lorsque deux types d’entités frères dans la hiérarchie ont une propriété portant le même nom, ils sont mappés à deux colonnes distinctes. Toutefois, si leur type est identique, ils peuvent être mappés à la même colonne de base de données :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>Configuration TPT (table par type)

> [!NOTE]
> La fonctionnalité table par type (TPT) a été introduite dans EF Core 5,0. Le type de table par point concret (TPC) est pris en charge par EF6, mais n’est pas encore pris en charge par EF Core.

Dans le modèle de mappage TPT, tous les types sont mappés à des tables individuelles. Les propriétés qui appartiennent uniquement à un type de base ou à un type dérivé sont stockées dans une table qui mappe à ce type. Les tables mappées à des types dérivés stockent également une clé étrangère qui joint la table dérivée à la table de base.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF crée le schéma de base de données suivant pour le modèle ci-dessus.

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> Si la contrainte de clé primaire est renommée, le nouveau nom est appliqué à toutes les tables mappées à la hiérarchie. les futures versions EF autorisent le changement de nom de la contrainte uniquement pour une table particulière lorsque le [problème 19970](https://github.com/dotnet/efcore/issues/19970) est résolu.

Si vous employez la configuration en bloc, vous pouvez récupérer le nom de colonne d’une table spécifique en appelant <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]

> [!WARNING]
> Dans de nombreux cas, TPT présente des performances inférieures par rapport à TPH. [Pour plus d’informations, consultez les documents sur les performances](xref:core/performance/modeling-for-performance#inheritance-mapping).
