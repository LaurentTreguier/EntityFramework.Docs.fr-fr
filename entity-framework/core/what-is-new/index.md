---
title: Mise en production et planification d’EF Core
description: Versions actuelles d’EF Core et détails sur le planning des versions ultérieures
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/index
ms.openlocfilehash: 18d8055840b5a38dc62d20e7e18c440b7360c9e4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128327"
---
# <a name="ef-core-releases-and-planning"></a>Mise en production et planification d’EF Core

## <a name="stable-releases"></a>Versions stables

| Libérer | Version cible de .NET Framework | Pris en charge jusqu’à | Liens
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | Mi-février, 2022 | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [Changements cassants](xref:core/what-is-new/ef-core-5.0/breaking-changes)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET Standard 2.0 | 3 décembre 2022 (LTS) | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | Expiré le 3 mars 2020 | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [Changements cassants](xref:core/what-is-new/ef-core-3.x/breaking-changes)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | Expiration le 23 décembre 2019 | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 21 août 2021 (LTS) | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | Expiration le 1er octobre 2018 | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | Expiration le 27 juin 2019 | [Annonce](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | Expiration le 27 juin 2019 | [Annonce](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

Pour plus d’informations sur les plateformes prises en charge par chacune des versions d’EF Core, voir [Plateformes prises en charge](xref:core/miscellaneous/platforms).

Pour plus d’informations sur les versions à expiration du support et les versions LTS (support à long terme), voir [Stratégie de support .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="guidance-on-updating-to-new-releases"></a>Aide sur la mise à jour vers les nouvelles versions

* La sécurité et d’autres bogues critiques sont corrigés dans les versions prises en charge. Utilisez toujours le dernier correctif d’une version donnée Par exemple, pour EF Core 2,1, utilisez 2.1. x pour le « x » le plus élevé disponible.
* Les mises à jour de versions majeures (par exemple, de EF Core 2 à EF Core 3) présentent souvent des changements cassants. Un test approfondi est recommandé dans ce cas de figure. Suivez les liens ci-dessus pour savoir comment gérer les changements cassants.
* Les mises à jour de versions mineures ne contiennent généralement pas de changements cassants. Toutefois, des tests poussés sont toujours recommandés, car les nouvelles fonctionnalités sont susceptibles d’introduire des régressions.

## <a name="release-planning-and-schedules"></a>Planification et planification des versions

Les versions d’EF Core s’alignent sur la [planification d’expédition .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

Les mises à jour correctives sont généralement envoyées tous les mois, mais avec un délai assez long.
Nous nous efforçons d’améliorer ce point.

Pour plus d’informations sur la façon dont nous sélectionnons les éléments à envoyer dans chaque version, consultez le [processus de planification des versions](xref:core/what-is-new/release-planning).
En règle générale, nous ne procédons pas à une planification détaillée au-delà de la prochaine version majeure ou mineure.

## <a name="ef-core-60"></a>EF Core 6,0

La prochaine version stable planifiée est **EF Core 6,0**, planifiée pour le **2021 novembre**.

Un [plan de haut niveau pour EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan) a été créé en suivant le [processus de planification des versions](xref:core/what-is-new/release-planning)documenté.

Vos commentaires sur la planification sont importants.
La meilleure façon d’indiquer l’importance d’un problème est de voter (pouce vers le haut 👍) pour ce problème sur GitHub.
Ces données sont ensuite intégrées dans le processus de planification de la prochaine version.

### <a name="get-it-now"></a>Obtenir maintenant

Les packages EF Core 6,0 sont **désormais disponibles** en tant que

* [Builds quotidiennes](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)
  * Toutes les fonctionnalités et tous les correctifs de bogues les plus récents. Généralement très stables ; 75000 + tests s’exécutent sur chaque Build.

En outre, à mesure que nous progressons, des préversions fréquentes sont envoyées à NuGet. Notez que les préversions sont en retard par rapport aux builds quotidiennes, mais qu’elles sont testées pour fonctionner avec les ASP.NET Core et les versions préliminaires .NET Core correspondantes.

Les préversions et les builds quotidiennes constituent un excellent moyen de détecter des problèmes et de fournir un retour d’expérience le plus tôt possible.
Plus tôt nous recevons ces commentaires, plus ils ont de chances d’être exploitables avant la version officielle suivante.
