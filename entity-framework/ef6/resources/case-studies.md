---
title: Études de cas pour Entity Framework-EF6
description: Études de cas pour Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: ca2315ac8aad8d28240f4bef6c962de0f63ac0df
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064664"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Études de cas Microsoft pour Entity Framework
Les études de cas sur cette page mettent en évidence quelques projets de production réels qui ont employé Entity Framework.
> [!NOTE]
> Les versions détaillées de ces études de cas ne sont plus disponibles sur le site Web de Microsoft. Par conséquent, les liens ont été supprimés.

## <a name="epicor"></a>Epicor espèrent accélérer
Epic est une grande entreprise de logiciels internationale (avec plus de 400 développeurs) qui développe des solutions ERP (Enterprise Resource Planning) pour les entreprises dans plus de 150 pays.
Leur produit phare, Epicy 9, est basé sur une architecture de Service-Oriented (SOA) à l’aide du .NET Framework.
Face à de nombreuses demandes des clients pour assurer la prise en charge de LINQ (Language-Integrated Query) et à réduire la charge sur leurs serveurs SQL principaux, l’équipe a décidé de mettre à niveau vers Visual Studio 2010 et le .NET Framework 4,0.
À l’aide de la Entity Framework 4,0, ils ont pu atteindre ces objectifs et simplifier de manière considérable le développement et la maintenance.
En particulier, la prise en charge de T4 enrichie de Entity Framework leur a permis de prendre le contrôle total de leur code généré et de créer automatiquement des fonctionnalités d’économie de performances telles que les requêtes précompilées et la mise en cache.

> «Nous avons récemment effectué des tests de performances avec le code existant et nous avons pu réduire les demandes à SQL Server de 90%.
Cela est dû à la ADO.NET Entity Framework 4». – Erik Johnson, Vice-Président, recherche de produits  

## <a name="veracity-solutions"></a>Solutions de véracité
Après avoir acquis un système logiciel de planification d’événements qui devait être difficile à gérer et s’étendre sur les solutions de véracité à long terme, a utilisé Visual Studio 2010 pour le réécrire sous la forme d’une application Internet puissante et facile à utiliser, basée sur Silverlight 4.
En utilisant les services RIA .NET, ils pouvaient créer rapidement une couche de service sur les Entity Framework qui ont évité la duplication de code et étaient autorisées pour la logique de validation et d’authentification commune à travers les niveaux.  

> «Nous avons été vendus sur le Entity Framework lors de sa première introduction, et le Entity Framework 4 s’est encore mieux amélioré.
Les outils sont améliorés et il est plus facile de manipuler les fichiers. edmx qui définissent le modèle conceptuel, le modèle de stockage et le mappage entre ces modèles... Avec la Entity Framework, je peux faire en sorte que cette couche d’accès aux données fonctionne dans une journée, et la mettre en œuvre pendant que je passe.
La Entity Framework est notre couche d’accès aux données de facto ; Je ne sais pas pourquoi quiconque ne l’utiliserait pas.» – Joe McBride, développeur senior

## <a name="nec-display-solutions-of-america"></a>Solutions d’affichage NEC d’Amérique
NEC souhaite accéder au marché pour la publicité sur place numérique, avec une solution pour tirer parti des annonceurs et des propriétaires de réseaux et augmenter ses revenus.
Pour ce faire, il a lancé une paire d’applications Web qui automatisent les processus manuels requis dans une campagne ad traditionnelle.
Les sites ont été créés à l’aide de ASP.NET, Silverlight 3, AJAX et WCF, ainsi que les Entity Framework dans la couche d’accès aux données pour communiquer avec SQL Server 2008.

> « Avec SQL Server, nous avons pensé que nous pourrions obtenir le débit nécessaire pour servir les annonceurs et les réseaux avec des informations en temps réel et la fiabilité pour s’assurer que les informations de nos applications stratégiques seraient toujours disponibles »-Mike Corcoran, directeur informatique

## <a name="darwin-dimensions"></a>Dimensions Darwin
À l’aide d’un large éventail de technologies Microsoft, l’équipe de Darwin a défini la création d’un portail d’avatar en ligne que les consommateurs pourraient utiliser pour créer des avatars époustouflants et réalistes à utiliser dans les jeux, les animations et les pages de réseaux sociaux.
Avec les avantages de productivité de la Entity Framework et l’extraction de composants tels que Windows Workflow Foundation (WF) et Windows Server AppFabric (cache d’application en mémoire hautement évolutif), l’équipe a pu fournir un produit étonnant en moins de 35% du temps de développement.
Bien que les membres de l’équipe soient répartis sur plusieurs pays, l’équipe suit un processus de développement agile avec des versions hebdomadaires.

 > «Nous essayons de ne pas créer de technologie pour les besoins de la technologie. Au démarrage, il est essentiel de tirer parti de la technologie qui permet de gagner du temps et de l’argent.
 .NET était le choix idéal pour un développement rapide et rentable.» – Zachary Olsen, architecte  

## <a name="silverware"></a>Silver
Avec plus de 15 ans d’expérience dans le développement de solutions de point de vente pour les groupes de restaurants de petite et moyenne taille, l’équipe de développement de Silver est chargée d’améliorer son produit avec davantage de fonctionnalités au niveau de l’entreprise afin d’attirer des chaînes de restaurant plus volumineuses.
À l’aide de la dernière version des outils de développement de Microsoft, ils ont pu créer la nouvelle solution quatre fois plus rapidement qu’auparavant.
Les nouvelles fonctionnalités clés telles que LINQ et le Entity Framework simplifient le passage de Crystal Reports à SQL Server 2008 et SQL Server Reporting Services (SSRS) pour leurs besoins en matière de stockage de données et de création de rapports.

> « La gestion efficace des données est essentielle au succès des Silvers, et c’est pourquoi nous avons décidé d’adopter SQL Reporting ». -Étant Nicholas Romanidis, directeur informatique/génie logiciel
