---
title: Modèles de génération de code de concepteur - EF6
description: Modèles de génération de code de concepteur dans Entity Framework 6.
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/index
ms.openlocfilehash: 9eb0a930d7b283e449f6b08c090c8483453de452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066484"
---
# <a name="designer-code-generation-templates"></a>Modèles de génération de code de concepteur
Quand vous créez un modèle à l’aide d’Entity Framework Designer, vos classes et le contexte dérivé sont générés automatiquement pour vous. En plus de la génération de code par défaut, nous fournissons également un nombre de modèles pouvant servir à personnaliser le code généré. Ces modèles sont fournis sous forme de modèles de texte T4, ce qui vous permet de les personnaliser si nécessaire.

Le code généré par défaut dépend de la version de Visual Studio dans laquelle vous avez créé votre modèle :

-   Les modèles créés dans Visual Studio 2012 et 2013 génèrent des classes d’entité OCT simples et un contexte qui dérive de la classe DbContext simplifiée.
-   Les modèles créés dans Visual Studio 2010 génèrent des classes d’entité qui dérivent d’EntityObject et un contexte qui dérive d’ObjectContext.
  > [!NOTE]    
  > Nous vous recommandons de passer au modèle DbContext Generator après l’ajout de votre modèle.

Cette page décrit les modèles disponibles et fournit des instructions pour ajouter un modèle à votre modèle.

## <a name="available-templates"></a>Modèles disponibles

Les modèles suivants sont fournis par l’équipe Entity Framework :

### <a name="dbcontext-generator"></a>DbContext Generator

Ce modèle génère des classes d’entité OCT simples et un contexte qui dérive de DbContext à l’aide d’EF6.
Il s’agit du modèle recommandé, sauf si vous avez besoin d’utiliser un des autres modèles répertoriés ci-dessous.
C’est également le modèle de génération de code que vous obtenez par défaut si vous utilisez les versions récentes de Visual Studio (Visual Studio 2013 et versions ultérieures) : quand vous créez un modèle, ce modèle est utilisé par défaut et les fichiers T4 (.tt) sont imbriqués sous votre fichier .edmx.

#### <a name="older-versions-of-visual-studio"></a>Versions précédentes de Visual Studio
- **Visual Studio 2012 :** Pour obtenir les modèles **EF 6.x DbContextGenerator**, vous devez installer la dernière version **d’Entity Framework Tools pour Visual Studio** (consultez la page [Obtenir Entity Framework](xref:ef6/fundamentals/install) pour plus d’informations).
- **Visual Studio 2010 :** Les modèles **EF 6.x DbContextGenerator** ne sont pas disponibles pour Visual Studio 2010.

#### <a name="dbcontext-generator-for-ef-5x"></a>DbContext Generator pour EF 5.x

Si vous utilisez une version antérieure du package NuGet EntityFramework (c’est-à-dire avec une version majeure égale à 5), vous devez utiliser le modèle **EF 5.x DbContext Generator**.

Si vous utilisez Visual Studio 2013 ou 2012, ce modèle est déjà installé.

Si vous utilisez Visual Studio 2010, vous devez sélectionner l’onglet **En ligne** quand vous ajoutez le modèle pour le télécharger à partir de la galerie Visual Studio. Vous pouvez aussi préinstaller le modèle directement à partir de la galerie Visual Studio. Comme les modèles sont inclus dans les versions ultérieures de Visual Studio, les versions de la galerie peuvent uniquement être installées sur Visual Studio 2010.

- [EF 5.x DbContext Generator for C#](https://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2)
- [EF 5.x DbContext Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03)
- [EF 5.x DbContext Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured)
- [EF 5.x DbContext Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614)

#### <a name="dbcontext-generator-for-ef-4x"></a>DbContext Generator pour EF 4.x

Si vous utilisez une version antérieure du package NuGet EntityFramework (c’est-à-dire avec une version majeure égale à 4), vous devez utiliser le modèle **EF 4.x DbContext Generator**. Il est disponible sous l’onglet **En ligne** quand vous ajoutez le modèle, ou vous pouvez le préinstaller directement à partir de la galerie Visual Studio.

- [EF 4.x DbContext Generator for C#](https://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2)
- [EF 4.x DbContext Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e)
- [EF 4.x DbContext Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073)
- [EF 4.x DbContext Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92)

### <a name="entityobject-generator"></a>EntityObject Generator

Ce modèle génère des classes d’entité qui dérivent d’EntityObject et un contexte qui dérive d’ObjectContext.

> [!NOTE]
> Utilisation de DbContext Generator

DbContext Generator est désormais le modèle recommandé pour les nouvelles applications. DbContext Generator tire parti de l’API DbContext plus simple. EntityObject Generator reste disponible pour prendre en charge les applications existantes.

**Visual Studio 2010, 2012 et 2013**

Vous devez sélectionner l’onglet **En ligne** quand vous ajoutez le modèle pour le télécharger à partir de la galerie Visual Studio. Vous pouvez aussi préinstaller le modèle directement à partir de la galerie Visual Studio.

- [EF 6.x EntityObject Generator for C#](https://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a)
- [EF 6.x EntityObject Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996)
- [EF 6.x EntityObject Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea)
- [EF 6.x EntityObject Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a)

**EntityObject Generator pour EF 5.x**


Si vous utilisez Visual Studio 2012 ou 2013, vous devez sélectionner l’onglet **En ligne** quand vous ajoutez le modèle pour le télécharger à partir de la galerie Visual Studio. Vous pouvez aussi préinstaller le modèle directement à partir de la galerie Visual Studio. Comme les modèles sont inclus dans Visual Studio 2010, les versions de la galerie peuvent uniquement être installées sur Visual Studio 2012 et 2013.

- [EF 5.x EntityObject Generator for C#](https://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339)
- [EF 5.x EntityObject Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [EF 5.x EntityObject Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30)
- [EF 5.x EntityObject Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41)

Si vous voulez utiliser la génération de code ObjectContext sans avoir à modifier le modèle, vous pouvez [revenir à la génération de code EntityObject](xref:ef6/modeling/designer/codegen/legacy-objectcontext).

Si vous utilisez Visual Studio 2010, ce modèle est déjà installé. Si vous créez un modèle dans Visual Studio 2010, il est utilisé par défaut, mais les fichiers .tt ne sont pas inclus dans votre projet. Pour personnaliser le modèle, vous devez l’ajouter à votre projet.

### <a name="self-tracking-entities-ste-generator"></a>Self-Tracking Entities (STE) Generator

Ce modèle génère des classes d’entité de suivi automatique et un contexte qui dérive d’ObjectContext. Dans une application EF, c’est le contexte qui est chargé de suivre les changements des entités. Toutefois, dans les scénarios multicouches, le contexte risque de ne pas être disponible sur la couche qui modifie les entités. Les entités de suivi automatique vous aident à suivre les changements de n’importe quelle couche. Pour plus d’informations, consultez [Entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index).

> [!NOTE]
> Modèle des entités de suivi automatique non recommandé

Nous ne recommandons plus d’utiliser le modèle des entités de suivi automatique dans les nouvelles applications, mais il reste disponible pour prendre en charge les applications existantes. Consultez [l’article sur les entités déconnectées](xref:ef6/fundamentals/disconnected-entities/index) afin de connaître les autres options que nous recommandons pour les scénarios multicouches.

> [!NOTE]
> Le modèle des entités de suivi automatique n’a pas de version EF 6.x.


> [!NOTE]
> Le modèle des entités de suivi automatique n’a pas de version Visual Studio 2013.

### <a name="visual-studio-2012"></a>Visual Studio 2012

Si vous utilisez Visual Studio 2012, vous devez sélectionner l’onglet **En ligne** quand vous ajoutez le modèle pour le télécharger à partir de la galerie Visual Studio. Vous pouvez aussi préinstaller le modèle directement à partir de la galerie Visual Studio. Comme les modèles sont inclus dans Visual Studio 2010, les versions de la galerie peuvent uniquement être installées sur Visual Studio 2012.

- [EF 5.x STE Generator for C#](https://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f)
- [EF 5.x STE Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c)
- [EF 5.x STE Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b)
- [EF 5.x STE Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

Si vous utilisez Visual Studio 2010, ce modèle est déjà installé.

### <a name="poco-entity-generator"></a>POCO Entity Generator

Ce modèle génère des classes d’entité OCT et un contexte qui dérive d’ObjectContext

> [!NOTE]
> Utilisation de DbContext Generator

DbContext Generator est désormais le modèle recommandé pour générer des classes OCT dans les nouvelles applications. DbContext Generator tire parti de la nouvelle API DbContext et peut générer des classes OCT plus simples. POCO Entity Generator reste disponible pour prendre en charge les applications existantes.

> [!NOTE]
> Le modèle OCT n’a pas de version EF 5.x ou EF 6.x.

> [!NOTE]
> Le modèle OCT n’a pas de version Visual Studio 2013.

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 et Visual Studio 2010

Vous devez sélectionner l’onglet **En ligne** quand vous ajoutez le modèle pour le télécharger à partir de la galerie Visual Studio. Vous pouvez aussi préinstaller le modèle directement à partir de la galerie Visual Studio.

- [EF 4.x POCO Generator for C#](https://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313)
- [EF 4.x POCO Generator for C# Web Sites](https://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f)
- [EF 4.x POCO Generator for VB.NET](https://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752)
- [EF 4.x POCO Generator for VB.NET Web Sites](https://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34)

### <a name="what-are-the-web-sites-templates"></a>Que sont les modèles « Web Sites »

Les modèles « Web Sites » (par ex., **EF 5.x DbContext Generator for C\# Web Sites**) sont destinés aux projets de site web créés via **Fichier -&gt; Nouveau -&gt; Site web...** . Ils diffèrent des applications web, créées via **Fichier -&gt; Nouveau -&gt; Projet...** , qui utilisent les modèles standard. Nous fournissons des modèles distincts, car le système de modèle d’élément dans Visual Studio le demande.

## <a name="using-a-template"></a>Utilisation d’un modèle

Pour commencer à utiliser un modèle de génération de code, cliquez avec le bouton droit sur un emplacement vide dans l’aire de conception dans EF Designer et sélectionnez **Ajouter un élément de génération de code...**.

![Ajouter un élément de génération de code](~/ef6/media/add-code-gen-item.png)

Si vous avez déjà installé le modèle à utiliser (ou qu’il a été inclus dans Visual Studio), il est disponible sous la section **Code** ou **Données** dans le menu à gauche.

![Modèle installé](~/ef6/media/installed.png)

Si vous n’avez pas installé le modèle, sélectionnez **En ligne** dans le menu à gauche et recherchez le modèle souhaité.

![Recherche du modèle](~/ef6/media/search.png) 

Si vous utilisez Visual Studio 2012, les nouveaux fichiers .tt sont imbriqués sous le fichier .edmx.*

> [!NOTE]
> Pour les modèles créés dans Visual Studio 2012, vous devez supprimer les modèles utilisés pour la génération de code par défaut, sinon vous obtenez des classes et un contexte en double. Les fichiers par défaut sont **&lt;nom du modèle&gt;.tt** et **&lt;nom du modèle&gt;.context.tt**. 

![Modèles Visual Studio 2012](~/ef6/media/vs2012-templates.png)

Si vous utilisez Visual Studio 2010, les fichiers tt sont ajoutés directement à votre projet.  

![Modèles Visual Studio 2010](~/ef6/media/vs2010-templates.png)
