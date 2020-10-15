---
title: Création d’un modèle - EF6
description: Création d’un modèle dans Entity Framework 6.
author: ajcvickers
ms.date: 07/05/2018
uid: ef6/modeling/index
ms.openlocfilehash: ba1aacc55feaf7bd8fdbc7667b64ba3842c538f9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065951"
---
# <a name="creating-a-model"></a>Création d’un modèle

Un modèle EF stocke la façon dont sont mappées les classes d’application et les propriétés aux colonnes et aux tables de base de données. Il existe deux façons principales de créer un modèle EF :

- **Utilisation de Code First** : Le développeur écrit du code pour spécifier le modèle. EF génère les modèles et les mappages au moment de l’exécution en fonction des classes d’entité et de la configuration de modèle supplémentaire fournie par le développeur.

- **Utilisation d’EF Designer** : Le développeur dessine des zones et des lignes pour spécifier le modèle à l’aide d’EF Designer. Le modèle qui en résulte est stocké au format XML dans un fichier avec l’extension EDMX. De manière générale, les objets de domaine de l’application sont générés automatiquement à partir du modèle conceptuel.

## <a name="ef-workflows"></a>Flux de travail EF

Ces deux approches peuvent servir à cibler une base de données existante ou créer une base de données, ce qui génère 4 flux de travail différents.
Découvrez celui qui vous convient le mieux :  

|                                           | Je veux simplement écrire du code...                                                                                                                   | Je veux utiliser un concepteur...                                                                                                                        |
|:------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Je crée une base de données**          | [Utilisez **Code First** pour définir votre modèle dans le code et générer une base de données.](xref:ef6/modeling/code-first/workflows/new-database)           | [Utilisez **Model First** pour définir votre modèle à l’aide de zones et de lignes, puis générer une base de données.](xref:ef6/modeling/designer/workflows/model-first)   |
| **J’ai besoin d’accéder à une base de données existante** | [Utilisez **Code First** pour créer un modèle basé sur le code mappé à une base de données existante.](xref:ef6/modeling/code-first/workflows/existing-database) | [Utilisez **Database First** pour créer un modèle de zones et de lignes mappé à une base de données existante.](xref:ef6/modeling/designer/workflows/database-first) |

### <a name="watch-the-video-what-ef-workflow-should-i-use"></a>Regardez la vidéo : Quel flux de travail EF utiliser ?

Cette courte vidéo explique les différences entre les flux de travail et vous guide dans votre choix.

**Présentée par** : [Rowan Miller](https://romiller.com/)

![Which Workflow Thumb](../media/whichworkflow-thumb.png) [WMV](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.wmv) | [MP4](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_mp4video_ChoseYourWorkflow.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.zip)

Si, après avoir regardé la vidéo, vous n’arrivez toujours pas à vous décider entre EF Designer ou Code First, apprenez à utiliser les deux !

## <a name="a-look-under-the-hood"></a>Rouages du système

Que vous utilisiez Code First ou EF Designer, un modèle EF a toujours plusieurs composants :

- Les objets de domaine de l’application ou les types d’entité eux-mêmes. Il s’agit de ce qu’on appelle généralement la couche objet

- Un modèle conceptuel comprenant des types d’entité et des relations propres à un domaine, décrits à l’aide [d’Entity Data Model](xref:ef6/resources/glossary#entity-data-model). Cette couche est souvent désignée par la lettre « C » pour _conceptuelle_.

- Un modèle de stockage représentant des tables, des colonnes et des relations comme défini dans la base de données. Cette couche est souvent désignée par la lettre « S » pour _stockage_.  

- Un mappage entre le modèle conceptuel et le schéma de base de données. Ce mappage est souvent appelé mappage « C-S ».

Le moteur de mappage d’Entity Framework s’appuie sur le mappage « C-S » pour transformer les opérations sur les entités (par ex., créer, lire, mettre à jour et supprimer) en opérations équivalentes sur les tables dans la base de données.

Le mappage entre le modèle conceptuel et les objets de l’application est souvent appelé mappage « O-C ». Par rapport au mappage « C-S », le mappage « O-C » est implicite et établit des relations un à un : les entités, propriétés et relations définies dans le modèle conceptuel doivent correspondre aux formes et types des objets .NET. À partir d’EF4 et version ultérieure, la couche objet peut contenir des objets simples avec des propriétés sans dépendance sur EF. Ces objets simples sont généralement désignés comme des objets CLR traditionnels (OCT) et le mappage des types et des propriétés est effectué en fonction des conventions de correspondance de nom. Auparavant, dans EF 3.5, des restrictions spécifiques s’appliquaient à la couche objet, par exemple, les entités devaient dériver de la classe EntityObject et porter des attributs EF pour implémenter le mappage « O-C ».
