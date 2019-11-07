---
title: Séquences-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: b810caaffa329bb5ad6f3486145d0ade9287eada
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656112"
---
# <a name="sequences"></a><span data-ttu-id="ecfb3-102">Séquences</span><span class="sxs-lookup"><span data-stu-id="ecfb3-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="ecfb3-103">La configuration indiquée dans cette section s’applique aux bases de données relationnelles en général.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="ecfb3-104">Les méthodes d’extension indiquées ici sont disponibles quand vous installez un fournisseur de base de données relationnelle (en raison du package partagé *Microsoft.EntityFrameworkCore.Relational*).</span><span class="sxs-lookup"><span data-stu-id="ecfb3-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="ecfb3-105">Une séquence génère des valeurs numériques séquentielles dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="ecfb3-106">Les séquences ne sont pas associées à une table spécifique.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="ecfb3-107">Conventions</span><span class="sxs-lookup"><span data-stu-id="ecfb3-107">Conventions</span></span>

<span data-ttu-id="ecfb3-108">Par Convention, les séquences ne sont pas introduites dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="ecfb3-109">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="ecfb3-109">Data Annotations</span></span>

<span data-ttu-id="ecfb3-110">Vous ne pouvez pas configurer une séquence à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ecfb3-111">API Fluent</span><span class="sxs-lookup"><span data-stu-id="ecfb3-111">Fluent API</span></span>

<span data-ttu-id="ecfb3-112">Vous pouvez utiliser l’API Fluent pour créer une séquence dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-112">You can use the Fluent API to create a sequence in the model.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Sequence.cs?name=Model&highlight=7)]

<span data-ttu-id="ecfb3-113">Vous pouvez également configurer un aspect supplémentaire de la séquence, par exemple son schéma, sa valeur de départ et son incrément.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceConfigured.cs?name=Sequence&highlight=7,8,9)]

<span data-ttu-id="ecfb3-114">Une fois qu’une séquence est introduite, vous pouvez l’utiliser pour générer des valeurs pour les propriétés de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="ecfb3-115">Par exemple, vous pouvez utiliser les [valeurs par défaut](default-values.md) pour insérer la valeur suivante à partir de la séquence.</span><span class="sxs-lookup"><span data-stu-id="ecfb3-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/SequenceUsed.cs?name=Default&highlight=13)]
