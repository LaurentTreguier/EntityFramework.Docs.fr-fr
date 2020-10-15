---
title: Vue d’ensemble de la journalisation et de l’interception-EF Core
description: Vue d’ensemble de la journalisation, des événements, des intercepteurs et des diagnostics pour EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066616"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="503fb-103">Vue d’ensemble de la journalisation et de l’interception</span><span class="sxs-lookup"><span data-stu-id="503fb-103">Overview of logging and interception</span></span>

<span data-ttu-id="503fb-104">Entity Framework Core (EF Core) contient plusieurs mécanismes permettant de générer des journaux, de répondre aux événements et d’obtenir des Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="503fb-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="503fb-105">Chacun d’eux étant adapté à différentes situations, il est important de sélectionner le mécanisme le mieux adapté à la tâche, même lorsque plusieurs mécanismes peuvent fonctionner.</span><span class="sxs-lookup"><span data-stu-id="503fb-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="503fb-106">Par exemple, un intercepteur de base de données peut être utilisé pour journaliser SQL, mais il est mieux géré par l’un des mécanismes spécifiques à la journalisation.</span><span class="sxs-lookup"><span data-stu-id="503fb-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="503fb-107">Cette page présente une vue d’ensemble de chacun de ces mécanismes et décrit les cas d’utilisation de chacun d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="503fb-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="503fb-108">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="503fb-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="503fb-109">Cette fonctionnalité a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="503fb-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="503fb-110">EF Core les journaux sont accessibles à partir de n’importe quel type d’application via l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="503fb-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="503fb-111">lors [de la configuration d’une instance DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="503fb-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="503fb-112">Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="503fb-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="503fb-113">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="503fb-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="503fb-114">Ce concept est semblable à <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6.</span><span class="sxs-lookup"><span data-stu-id="503fb-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="503fb-115">Pour plus d’informations, consultez [journalisation simple](xref:core/miscellaneous/events/simple-logging) .</span><span class="sxs-lookup"><span data-stu-id="503fb-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
