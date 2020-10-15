---
title: Amélioration des performances de démarrage avec NGen-EF6
description: Amélioration des performances de démarrage avec NGen dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: a58ffc1a4da1cdb0ae12366ab1ec0139141b3a7f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065418"
---
# <a name="improving-startup-performance-with-ngen"></a>Amélioration des performances de démarrage avec NGen
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Le .NET Framework prend en charge la génération d’images natives pour les applications et les bibliothèques managées afin d’aider les applications à démarrer plus rapidement et, dans certains cas, à utiliser moins de mémoire. Les images natives sont créées en traduisant des assemblys de code managé en fichiers contenant des instructions machine natives avant l’exécution de l’application, ce qui allège le compilateur .NET JIT (juste-à-temps) d’avoir à générer les instructions natives au moment de l’exécution de l’application.  

Avant la version 6, les bibliothèques principales du runtime EF faisaient partie du .NET Framework et les images natives ont été générées automatiquement. Depuis la version 6, l’intégralité du runtime EF a été combinée dans le package NuGet EntityFramework. Les images natives doivent maintenant être générées à l’aide de l’outil de ligne de commande NGen.exe pour obtenir des résultats similaires.  

Les observations empiriques montrent que les images natives des assemblys du runtime EF peuvent réduire de 1 à 3 secondes le temps de démarrage de l’application.  

## <a name="how-to-use-ngenexe"></a>Comment utiliser NGen.exe  

La fonction la plus simple de l’outil NGen.exe consiste à « installer » (autrement dit, à créer et à conserver sur le disque) des images natives pour un assembly et toutes ses dépendances directes. Voici comment y parvenir :  

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Remplacez le répertoire de travail actuel par l’emplacement des assemblys pour lesquels vous souhaitez générer des images natives :

   ``` console
   cd <*Assemblies location*>  
   ```

3. En fonction de votre système d’exploitation et de la configuration de l’application, vous devrez peut-être générer des images natives pour l’architecture 32 bits, l’architecture 64 bits ou pour les deux.

   Pour une exécution de 32 bits :

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   Pour une exécution de 64 bits :
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> La génération d’images natives pour une architecture incorrecte est une erreur très courante. En cas de doute, vous pouvez simplement générer des images natives pour toutes les architectures qui s’appliquent au système d’exploitation installé sur l’ordinateur.  

NGen.exe prend également en charge d’autres fonctions telles que la désinstallation et l’affichage des images natives installées, la mise en file d’attente de la génération de plusieurs images, etc. Pour plus d’informations sur l’utilisation, consultez la [ documentationNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx).  

## <a name="when-to-use-ngenexe"></a>Quand utiliser NGen.exe  

Lorsqu’il s’agit de déterminer les assemblys pour lesquels générer des images natives dans une application basée sur EF version 6 ou ultérieure, vous devez prendre en compte les options suivantes :  

- **L’assembly principal du runtime EF, EntityFramework.dll**: une application basée sur EF classique exécute une quantité significative de code à partir de cet assembly au démarrage ou lors de son premier accès à la base de données. Par conséquent, la création d’images natives de cet assembly produit les plus grandes améliorations des performances de démarrage.  
- **Tout assembly de fournisseur EF utilisé par votre application : le**temps de démarrage peut également tirer parti de la génération d’images natives. Par exemple, si l’application utilise le fournisseur EF pour SQL Server vous souhaitez générer une image native pour EntityFramework.SqlServer.dll.  
- Les **assemblys de votre application et les autres dépendances**: la [documentationNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx) aborde les critères généraux permettant de choisir les assemblys pour lesquels générer des images natives et l’impact des images natives sur la sécurité, des options avancées telles que la « liaison matérielle », des scénarios tels que l’utilisation d’images natives dans des scénarios de débogage et de profilage, etc.  

> [!TIP]
> Veillez à mesurer avec soin l’impact de l’utilisation d’images natives sur les performances de démarrage et les performances globales de votre application, et comparez-les aux exigences réelles. Bien que les images natives permettent généralement d’améliorer les performances de démarrage et, dans certains cas, de réduire l’utilisation de la mémoire, tous les scénarios ne sont pas avantageux. Par exemple, lors d’une exécution stable de l’État (autrement dit, une fois que toutes les méthodes utilisées par l’application ont été appelées au moins une fois), le code généré par le compilateur JIT peut, en fait, produire des performances légèrement meilleures que les images natives.  

## <a name="using-ngenexe-in-a-development-machine"></a>Utilisation de NGen.exe dans un ordinateur de développement  

Pendant le développement, le compilateur JIT .NET offrira le meilleur compromis global pour le code qui change fréquemment. La génération d’images natives pour les dépendances compilées telles que les assemblys du runtime EF peut accélérer le développement et le test en coupant quelques secondes au début de chaque exécution.  

L’emplacement du package NuGet pour la solution est un bon emplacement pour rechercher les assemblys du runtime EF. Par exemple, pour une application utilisant EF 6.0.2 avec SQL Server et ciblant .NET 4,5 ou une version ultérieure, vous pouvez taper la commande suivante dans une fenêtre d’invite de commandes (n’oubliez pas de l’ouvrir en tant qu’administrateur) :  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> Cela tire parti du fait que l’installation des images natives pour le fournisseur EF pour SQL Server entraîne également l’installation par défaut des images natives pour l’assembly de runtime EF principal. Cela fonctionne parce que NGen.exe pouvez détecter que EntityFramework.dll est une dépendance directe de l’assembly EntityFramework.SqlServer.dll situé dans le même répertoire.  

## <a name="creating-native-images-during-setup"></a>Création d’images natives pendant l’installation  

WiX Toolkit prend en charge la mise en file d’attente de la génération d’images natives pour les assemblys managés au cours de l’installation, comme expliqué dans ce [Guide de procédure](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html). Une autre solution consiste à créer une tâche d’installation personnalisée qui exécute la commande NGen.exe.  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>Vérification de l’utilisation des images natives pour EF  

Vous pouvez vérifier qu’une application spécifique utilise un assembly natif en recherchant les assemblys chargés ayant l’extension « .ni.dll » ou « .ni.exe ». Par exemple, une image native de l’assembly de Runtime principal d’EF sera appelée EntityFramework.ni.dll. Un moyen simple d’inspecter les assemblys .NET chargés d’un processus consiste à utiliser [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).  

## <a name="other-things-to-be-aware-of"></a>Autres éléments à connaître  

La **création d’une image native d’un assembly ne doit pas être confondue avec l’inscription de l’assembly dans le [GAC (global assembly cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**. NGen.exe permet de créer des images d’assemblys qui ne se trouvent pas dans le GAC et, en fait, plusieurs applications qui utilisent une version spécifique d’EF peuvent partager la même image native. Bien que Windows 8 puisse créer automatiquement des images natives pour les assemblys placés dans le GAC, l’exécution d’EF est optimisée pour être déployée en même temps que votre application. nous vous déconseillons de l’inscrire dans le GAC, car cela a un impact négatif sur la résolution de l’assembly et la maintenance de vos applications entre autres aspects.  
