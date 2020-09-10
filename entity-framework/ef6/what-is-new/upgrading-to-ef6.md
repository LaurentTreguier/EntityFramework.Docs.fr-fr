---
title: Mise à niveau vers Entity Framework 6-EF6
description: Mise à niveau vers Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: b27504e3b18c668a1c41142b9cd5e697944cb5fa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619712"
---
# <a name="upgrading-to-entity-framework-6"></a>Mise à niveau vers Entity Framework 6

Dans les versions précédentes d’EF, le code était divisé entre les bibliothèques principales (principalement System.Data.Entity.dll) fournies dans le cadre des bibliothèques .NET Framework et hors bande (prêtes à l’emploi) (principalement EntityFramework.dll) fournies dans un package NuGet. EF6 prend le code des bibliothèques principales et l’intègre dans les bibliothèques OOB. Cela était nécessaire pour permettre à EF d’être rendu Open source et pour pouvoir évoluer à un rythme différent de .NET Framework. Cela est dû au fait que les applications devront être reconstruites sur les types déplacés.

Cela doit être simple pour les applications qui utilisent DbContext comme fourni dans EF 4,1 et versions ultérieures. Un peu plus de travail est nécessaire pour les applications qui utilisent ObjectContext, mais il n’est pas encore difficile de le faire.

Voici une liste de contrôle des éléments que vous devez effectuer pour mettre à niveau une application existante vers EF6.

## <a name="1-install-the-ef6-nuget-package"></a>1. installer le package NuGet EF6

Vous devez effectuer une mise à niveau vers le nouveau runtime Entity Framework 6.

1. Cliquez avec le bouton droit sur votre projet et sélectionnez **gérer les packages NuGet...**  
2. Sous l’onglet **en ligne** , sélectionnez **EntityFramework** , puis cliquez sur **installer** .  
   > [!NOTE]
   > Si une version précédente du package NuGet EntityFramework a été installée, cette opération est mise à niveau vers EF6.

Vous pouvez également exécuter la commande suivante à partir de la console du gestionnaire de package :

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. Assurez-vous que les références d’assembly aux System.Data.Entity.dll sont supprimées

L’installation du package NuGet EF6 doit supprimer automatiquement toutes les références à System. Data. Entity de votre projet.

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. permuter n’importe quel modèle EF Designer (EDMX) pour utiliser la génération de code EF 6. x

Si vous avez créé des modèles avec le concepteur EF, vous devrez mettre à jour les modèles de génération de code pour générer du code compatible EF6.

> [!NOTE]
> Il n’existe actuellement que les modèles de générateur de DbContext EF 6. x disponibles pour Visual Studio 2012 et 2013.

1. Supprimer les modèles de génération de code existants. Ces fichiers sont généralement nommés ** \<edmx_file_name\> . TT** et ** \<edmx_file_name\> . Context.tt** et sont imbriqués sous votre fichier edmx dans Explorateur de solutions. Vous pouvez sélectionner les modèles dans Explorateur de solutions et appuyer sur la touche **Suppr** pour les supprimer.  
   > [!NOTE]
   > Dans les projets de site Web, les modèles ne sont pas imbriqués sous votre fichier edmx, mais sont listés en même temps dans Explorateur de solutions.  

   > [!NOTE]
   > Dans les projets VB.NET, vous devez activer « afficher tous les fichiers » pour pouvoir voir les fichiers de modèles imbriqués.
2. Ajoutez le modèle de génération de code EF 6. x approprié. Ouvrez votre modèle dans le concepteur EF, cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Ajouter un élément de génération de code...**
    - Si vous utilisez l’API DbContext (recommandé), le **Générateur de DbContext EF 6. x** sera disponible sous l’onglet **données** .  
      > [!NOTE]
      > Si vous utilisez Visual Studio 2012, vous devrez installer les outils EF 6 pour obtenir ce modèle. Pour plus d’informations, consultez [obtenir Entity Framework](xref:ef6/fundamentals/install) .  

    - Si vous utilisez l’API ObjectContext, vous devrez sélectionner l’onglet **Online** et rechercher le **Générateur EntityObject EF 6. x**.  
3. Si vous avez appliqué des personnalisations aux modèles de génération de code, vous devez les réappliquer aux modèles mis à jour.

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. mettre à jour les espaces de noms pour tous les types EF de base utilisés

Les espaces de noms pour DbContext et les types Code First n’ont pas changé. Cela signifie que pour de nombreuses applications qui utilisent EF 4,1 ou une version ultérieure, vous n’aurez pas besoin de modifier quoi que ce soit.

Les types comme ObjectContext qui étaient précédemment dans System.Data.Entity.dll ont été déplacés vers de nouveaux espaces de noms. Cela signifie que vous devrez peut-être mettre à jour vos directives *using* ou *Import* pour créer des EF6.

La règle générale pour les modifications d’espace de noms est que tout type dans System. Data. * est déplacé vers System. Data. Entity. Core. *. En d’autres termes, il suffit d’insérer **Entity. Core.** après System. Data. Par exemple :

- System. Data. EntityException => System. Data. **Entity. Core**. EntityException  
- System. Data. Objects. ObjectContext => System. Data. **Entity. Core**. Objects. ObjectContext  
- System. Data. Objects. DataClasses. RelationshipManager => System. Data. **Entity. Core**. Objets. DataClasses. RelationshipManager  

Ces types se trouvent dans les espaces de noms de *base* , car ils ne sont pas utilisés directement pour la plupart des applications basées sur DbContext. Certains types qui faisaient partie de System.Data.Entity.dll sont toujours utilisés communément et directement pour les applications basées sur DbContext. ils n’ont donc pas été déplacés dans les espaces de noms de *base* . Ces règles sont les suivantes :

- System. Data. EntityState => System. Data. **Entité**. EntityState  
- System. Data. Objects. DataClasses. EdmFunctionAttribute => System. Data. **Entité. DbFunctionAttribute**  
  > [!NOTE]
  > Cette classe a été renommée ; une classe portant l’ancien nom existe toujours et fonctionne, mais elle est désormais marquée comme obsolète.  
- System. Data. Objects. EntityFunctions => System. Data. **Entité. DbFunctions**  
  > [!NOTE]
  > Cette classe a été renommée ; une classe portant l’ancien nom existe toujours et fonctionne, mais elle est désormais marquée comme obsolète.)  
- Les classes spatiales (par exemple, DbGeography, DbGeometry) ont été déplacées de System. Data. spatial => System. Data. **Entité**. Adjacent

> [!NOTE]
> Certains types de l’espace de noms System. Data se trouvent dans System.Data.dll qui n’est pas un assembly EF. Ces types n’ont pas été déplacés et leurs espaces de noms restent inchangés.
