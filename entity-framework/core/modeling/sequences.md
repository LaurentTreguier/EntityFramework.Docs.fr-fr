---
title: Séquences-EF Core
description: Configuration de séquences dans un modèle de Entity Framework Core
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: e388bde11b1251564b37227e8884d2b8c34dc02e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128522"
---
# <a name="sequences"></a><span data-ttu-id="f45e5-103">Séquences</span><span class="sxs-lookup"><span data-stu-id="f45e5-103">Sequences</span></span>

> [!NOTE]
> <span data-ttu-id="f45e5-104">Les séquences sont une fonctionnalité généralement prise en charge uniquement par les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="f45e5-104">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="f45e5-105">Si vous utilisez une base de données non relationnelle telle que Cosmos, consultez la documentation de votre base de données sur la génération de valeurs uniques.</span><span class="sxs-lookup"><span data-stu-id="f45e5-105">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="f45e5-106">Une séquence génère des valeurs numériques séquentielles uniques dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f45e5-106">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="f45e5-107">Les séquences ne sont pas associées à une table spécifique, et plusieurs tables peuvent être configurées pour dessiner des valeurs à partir de la même séquence.</span><span class="sxs-lookup"><span data-stu-id="f45e5-107">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="f45e5-108">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="f45e5-108">Basic usage</span></span>

<span data-ttu-id="f45e5-109">Vous pouvez configurer une séquence dans le modèle, puis l’utiliser pour générer des valeurs pour les propriétés :</span><span class="sxs-lookup"><span data-stu-id="f45e5-109">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="f45e5-110">Notez que le SQL spécifique utilisé pour générer une valeur à partir d’une séquence est spécifique à la base de données ; l’exemple ci-dessus fonctionne sur SQL Server mais échoue sur les autres bases de données.</span><span class="sxs-lookup"><span data-stu-id="f45e5-110">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="f45e5-111">Pour plus d’informations, consultez la documentation de votre base de données spécifique.</span><span class="sxs-lookup"><span data-stu-id="f45e5-111">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="f45e5-112">Configuration des paramètres de séquence</span><span class="sxs-lookup"><span data-stu-id="f45e5-112">Configuring sequence settings</span></span>

<span data-ttu-id="f45e5-113">Vous pouvez également configurer d’autres aspects de la séquence, tels que son schéma, sa valeur de départ, son incrément, etc. :</span><span class="sxs-lookup"><span data-stu-id="f45e5-113">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
