---
title: Enregistrement de données - EF Core
description: Vue d’ensemble de l’enregistrement des données avec Entity Framework Core.
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: 13e7c470e71f9cf9a1112aad18a8902e2235cef1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616314"
---
# <a name="saving-data"></a>Enregistrement de données

Chaque instance de contexte a un `ChangeTracker` qui est responsable du suivi des modifications à écrire dans la base de données. Quand vous apportez des modifications à des instances de vos classes d’entité, ces modifications sont enregistrées dans le `ChangeTracker`, puis écrites dans la base de données quand vous appelez `SaveChanges`. Le fournisseur de base de données est chargé de traduire les modifications en opérations de base de données (par exemple les commandes `INSERT`, `UPDATE` et `DELETE` pour une base de données relationnelle).
