---
title: Migrations Code First dans les environnements d’équipe-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4c2d9a95-de6f-4e97-9738-c1f8043eff69
ms.openlocfilehash: 7ab66a8ec29b60bb2e0102fbca5745d352b07325
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370407"
---
# <a name="code-first-migrations-in-team-environments"></a>Migrations Code First dans les environnements d’équipe
> [!NOTE]
> Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base. Si ce n’est pas le cas, vous devez lire [migrations code First](~/ef6/modeling/code-first/migrations/index.md) avant de continuer.

## <a name="grab-a-coffee-you-need-to-read-this-whole-article"></a>Prenez un café, vous devez lire cet article entier

Les problèmes dans les environnements d’équipe concernent essentiellement la fusion des migrations lorsque deux développeurs ont généré des migrations dans leur base de code locale. Bien que les étapes à suivre pour les résoudre soient assez simples, elles vous obligent à comprendre le fonctionnement des migrations. N’hésitez pas à passer à la fin. Prenez le temps de lire tout l’article pour vous assurer que vous êtes bien en bonne voie.

## <a name="some-general-guidelines"></a>Quelques recommandations générales

Avant de nous plonger dans la manière de gérer la fusion des migrations générées par plusieurs développeurs, voici quelques recommandations générales qui vous aideront à vous préparer à la réussite.

### <a name="each-team-member-should-have-a-local-development-database"></a>Chaque membre de l’équipe doit avoir une base de données de développement local

Migrations utilise la table ** \_ \_ MigrationsHistory** pour stocker les migrations qui ont été appliquées à la base de données. Si vous avez plusieurs développeurs qui génèrent des migrations différentes en tentant de cibler la même base de données (et donc partager une table ** \_ \_ MigrationsHistory** ), les migrations vont être très confuses.

Bien entendu, si vous avez des membres de l’équipe qui ne génèrent pas de migrations, il n’y a aucun problème de partage d’une base de données de développement central.

### <a name="avoid-automatic-migrations"></a>Évitez les migrations automatiques

Le résultat est que les migrations automatiques semblent initialement bonnes dans les environnements d’équipe, mais en réalité, elles ne fonctionnent pas. Si vous souhaitez savoir pourquoi, continuez à lire, sinon, vous pouvez passer à la section suivante.

La migration automatique vous permet de mettre à jour votre schéma de base de données pour qu’il corresponde au modèle actuel sans avoir à générer des fichiers de code (migrations basées sur le code). Les migrations automatiques fonctionnent très bien dans un environnement d’équipe si vous ne les avez jamais utilisées et que vous n’avez jamais généré de migrations basées sur du code. Le problème est que les migrations automatiques sont limitées et ne gèrent pas un certain nombre d’opérations (renommages de propriétés/colonnes, déplacement de données vers une autre table, etc.). Pour gérer ces scénarios, vous devez générer des migrations basées sur du code (et modifier le code de génération de modèles automatique) qui sont mélangées entre les modifications gérées par les migrations automatiques. Il est ainsi presque impossible de fusionner les modifications lorsque deux développeurs archivent des migrations.

## <a name="screencasts"></a>Captures

Si vous préférez regarder une capture vidéo plutôt que lire cet article, les deux vidéos suivantes couvrent le même contenu que cet article.

### <a name="video-one-migrations---under-the-hood"></a>Vidéo 1 : « migrations-en coulisses »

[Cet](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) enregistrement contient des informations sur le mode de suivi et d’utilisation des informations sur le modèle pour détecter les modifications du modèle.

### <a name="video-two-migrations---team-environments"></a>Vidéo 2 : « migrations-environnements d’équipe »

En s’appuyant sur les concepts de la vidéo précédente, [cette capture](https://channel9.msdn.com/blogs/ef/migrations-team-environments) vidéo couvre les problèmes qui surviennent dans un environnement d’équipe et comment les résoudre.

## <a name="understanding-how-migrations-works"></a>Comprendre le fonctionnement des migrations

Pour utiliser avec succès des migrations dans un environnement d’équipe, il est essentiel de comprendre comment les migrations suivent et utilisent les informations relatives au modèle pour détecter les modifications du modèle.

### <a name="the-first-migration"></a>La première migration

Lorsque vous ajoutez la première migration à votre projet, vous exécutez un fichier comme **Add-migration en premier** dans la console du gestionnaire de package. Les étapes de haut niveau effectuées par cette commande sont illustrées ci-dessous.

![Première migration](~/ef6/media/firstmigration.png)

Le modèle actuel est calculé à partir de votre code (1). Les objets de base de données requis sont ensuite calculés par le modèle différent (2). dans la mesure où il s’agit de la première migration, le modèle diffère simplement de l’utilisation d’un modèle vide pour la comparaison. Les modifications requises sont transmises au générateur de code pour générer le code de migration requis (3) qui est ensuite ajouté à votre solution Visual Studio (4).

Outre le code de migration proprement dit stocké dans le fichier de code principal, les migrations génèrent également des fichiers code-behind supplémentaires. Ces fichiers sont des métadonnées qui sont utilisées par les migrations et ne sont pas des éléments que vous devez modifier. L’un de ces fichiers est un fichier de ressources (. resx) qui contient un instantané du modèle au moment de la création de la migration. Vous verrez comment cela est utilisé à l’étape suivante.

À ce stade, vous devriez probablement exécuter **Update-Database** pour appliquer vos modifications à la base de données, puis vous allez implémenter d’autres zones de votre application.

### <a name="subsequent-migrations"></a>Migrations suivantes

Plus tard, vous revenez et apportez des modifications à votre modèle. dans notre exemple, nous allons ajouter une propriété **URL** au **blog**. Vous devez ensuite émettre une commande telle que **Add-migration AddUrl** pour générer une migration en vue d’appliquer les modifications de base de données correspondantes. Les étapes de haut niveau effectuées par cette commande sont illustrées ci-dessous.

![Deuxième migration](~/ef6/media/secondmigration.png)

Tout comme la dernière fois, le modèle actuel est calculé à partir du code (1). Toutefois, cette fois, il existe des migrations afin que le modèle précédent soit récupéré de la dernière migration (2). Ces deux modèles sont comparés pour rechercher les modifications de base de données requises (3), puis le processus se termine comme avant.

Ce même processus est utilisé pour toutes les migrations supplémentaires que vous ajoutez au projet.

### <a name="why-bother-with-the-model-snapshot"></a>Pourquoi ne pas vous soucier de l’instantané de modèle ?

Vous vous demandez peut-être pourquoi EF les deux avec l’instantané de modèle : pourquoi ne pas simplement examiner la base de données. Dans ce cas, lisez la suite. Si vous n’êtes pas intéressé, vous pouvez ignorer cette section.

EF conserve l’instantané de modèle pour plusieurs raisons :

-   Cela permet à votre base de données de dérive du modèle EF. Ces modifications peuvent être apportées directement dans la base de données, ou vous pouvez modifier le code de génération de modèles automatique dans vos migrations pour effectuer les modifications. Voici quelques exemples de cette pratique :
    -   Vous souhaitez ajouter une colonne insérée et mise à jour à une ou plusieurs de vos tables, mais vous ne souhaitez pas inclure ces colonnes dans le modèle EF. Si les migrations examinent la base de données, elle essaiera continuellement de supprimer ces colonnes chaque fois que vous générez une migration par génération de modèles automatique. À l’aide de l’instantané de modèle, EF détecte uniquement les modifications légitimes apportées au modèle.
    -   Vous souhaitez modifier le corps d’une procédure stockée utilisée pour les mises à jour afin d’inclure une certaine journalisation. Si les migrations ont examiné cette procédure stockée à partir de la base de données, elle tentera continuellement de la réinitialiser en rétablissant la définition attendue par EF. À l’aide de l’instantané de modèle, EF n’effectue jamais de génération de code automatique pour modifier la procédure stockée lorsque vous modifiez la forme de la procédure dans le modèle EF.
    -   Ces mêmes principes s’appliquent à l’ajout d’index supplémentaires, y compris des tables supplémentaires dans votre base de données, au mappage d’EF à une vue de base de données qui se trouve sur une table, etc.
-   Le modèle EF contient plus que la seule forme de la base de données. Le fait de disposer de l’ensemble du modèle permet aux migrations d’examiner les informations sur les propriétés et les classes de votre modèle et la façon dont elles sont mappées aux colonnes et aux tables. Ces informations permettent aux migrations d’être plus intelligentes dans le code généré par le modèle. Par exemple, si vous modifiez le nom de la colonne mappée aux migrations, vous pouvez détecter le changement de nom en vérifiant qu’il s’agit de la même propriété, ce qui ne peut pas être fait si vous n’avez que le schéma de base de données. 

## <a name="what-causes-issues-in-team-environments"></a>Causes des problèmes dans les environnements d’équipe

Le flux de travail abordé dans la section précédente fonctionne très bien lorsque vous êtes un développeur unique qui travaille sur une application. Il fonctionne également bien dans un environnement d’équipe si vous êtes la seule personne à apporter des modifications au modèle. Dans ce scénario, vous pouvez apporter des modifications au modèle, générer des migrations et les envoyer à votre contrôle de code source. D’autres développeurs peuvent synchroniser vos modifications et exécuter **Update-Database** pour appliquer les modifications de schéma.

Les problèmes commencent à se produire lorsque plusieurs développeurs modifient le modèle EF et envoient le contrôle de code source en même temps. Le manque d’EF est une méthode de premier ordre pour fusionner vos migrations locales avec les migrations qu’un autre développeur a soumises au contrôle de code source depuis la dernière synchronisation.

## <a name="an-example-of-a-merge-conflict"></a>Exemple de conflit de fusion

Tout d’abord, examinons un exemple concret de ce type de conflit de fusion. Nous poursuivrons avec l’exemple que nous avons vu précédemment. En guise de point de départ, supposons que les modifications de la section précédente ont été archivées par le développeur d’origine. Nous allons suivre deux développeurs lorsqu’ils apportent des modifications à la base de code.

Nous allons suivre le modèle EF et les migrations à l’aide d’un certain nombre de modifications. Pour un point de départ, les deux développeurs sont synchronisés avec le référentiel de contrôle de code source, comme illustré dans le graphique suivant.

![Point de départ](~/ef6/media/startingpoint.png)

Developer \# 1 et Developer \# 2 apporte désormais certaines modifications au modèle EF dans leur base de code locale. Developer \# 1 ajoute une propriété **Rating** au **blog** , et génère une migration **AddRating** pour appliquer les modifications à la base de données. Developer \# 2 ajoute une propriété **Readers** au **blog** , et génère la migration **AddReaders** correspondante. Les deux développeurs exécutent **Update-Database**pour appliquer les modifications à leurs bases de données locales, puis poursuivre le développement de l’application.

> [!NOTE]
> Les migrations sont précédées d’un horodateur. notre graphique représente donc que la migration AddReaders de Developer \# 2 vient après la migration de AddRating de Developer \# 1. La \# première fois que le développeur 1 ou \# 2 a généré la migration, il ne fait aucune différence entre les problèmes de travail d’une équipe ou le processus de fusion que nous allons examiner dans la section suivante.

![Modifications locales](~/ef6/media/localchanges.png)

C’est un jour de chance pour le développeur \# 1, car il s’agit d’envoyer les modifications en premier. Étant donné que personne d’autre n’a archivé depuis qu’il a synchronisé son référentiel, il peut simplement envoyer les modifications sans effectuer de fusion.

![Envoyer](~/ef6/media/submit.png)

Maintenant, il est temps pour le développeur \# 2 de l’envoyer. Ils ne sont pas si heureux. Étant donné qu’un autre utilisateur a soumis des modifications depuis qu’il a été synchronisé, il devra extraire les modifications et les fusionner. Le système de contrôle de code source sera probablement en mesure de fusionner automatiquement les modifications au niveau du code, car elles sont très simples. L’état du \# référentiel local du développeur 2 après la synchronisation est illustré dans le graphique suivant. 

![Extraction](~/ef6/media/pull.png)

À ce niveau, Developer \# 2 peut exécuter **Update-Database** , qui détectera la nouvelle migration **AddRating** (qui n’a pas été appliquée à la \# base de données de Developer 2) et l’appliquera. À présent, la colonne **Rating** est ajoutée à la table **blogs** et la base de données est synchronisée avec le modèle.

Toutefois, il existe quelques problèmes :

1.  Bien que **Update-Database** applique la migration **AddRating** , il génère également un avertissement : *Impossible de mettre à jour la base de données pour qu’elle corresponde au modèle actuel, car des modifications sont en attente et la migration automatique est désactivée...*
    Le problème est que l’instantané de modèle stocké dans la dernière migration (**AddReader**) ne contient pas la propriété **Rating** sur le **blog** (puisqu’il ne fait pas partie du modèle lors de la création de la migration). Code First détecte que le modèle de la dernière migration ne correspond pas au modèle actuel et déclenche l’avertissement.
2.  L’exécution de l’application génère une exception InvalidOperationException indiquant que «*le modèle sauvegardant le contexte «BloggingContext » a changé depuis la création de la base de données. Envisagez d’utiliser Migrations Code First pour mettre à jour la base de données...»*
    Là encore, le problème est que l’instantané de modèle stocké dans la dernière migration ne correspond pas au modèle actuel.
3.  Enfin, l’exécution d' **Add-migration** génère désormais une migration vide (puisqu’il n’y a aucune modification à appliquer à la base de données). Toutefois, étant donné que migrations compare le modèle actuel à celui de la dernière migration (qui ne dispose pas de la propriété **Rating** ), il génère en réalité un autre appel **AddColumn** à ajouter dans la colonne **Rating** . Bien entendu, cette migration échouerait pendant **Update-Database** , car la colonne d' **évaluation** existe déjà.

## <a name="resolving-the-merge-conflict"></a>Résolution du conflit de fusion

La bonne nouvelle, c’est qu’il n’est pas trop difficile de gérer la fusion manuellement, à condition que vous compreniez le fonctionnement des migrations. Par conséquent, si vous avez ignoré cette section... vous devez d’abord revenir en arrière et lire le reste de l’article.

Il existe deux options : la plus simple consiste à générer une migration vide qui a le modèle actuel correct en tant qu’instantané. La deuxième option consiste à mettre à jour l’instantané dans la dernière migration pour avoir l’instantané de modèle approprié. La deuxième option est un peu plus difficile et ne peut pas être utilisée dans tous les scénarios, mais elle est également plus propre, car elle n’implique pas l’ajout d’une migration supplémentaire.

### <a name="option-1-add-a-blank-merge-migration"></a>Option 1 : ajouter une migration « fusionner » vide

Dans cette option, nous générons une migration vierge uniquement dans le but de vérifier que la dernière migration est stockée dans l’instantané de modèle approprié.

Cette option peut être utilisée, quelle que soit la personne qui a généré la dernière migration. Dans l’exemple, nous avons suivi \# le développement de la dernière migration, et cela a pris en charge la fusion. Toutefois, ces mêmes étapes peuvent être utilisées si \# le développeur 1 a généré la dernière migration. Les étapes s’appliquent également si plusieurs migrations sont impliquées : nous avons juste cherché deux pour la conserver simple.

Le processus suivant peut être utilisé pour cette approche, à partir du moment où vous réalisez que vous réalisez des modifications qui doivent être synchronisées à partir du contrôle de code source.

1.  Vérifiez que toutes les modifications de modèle en attente dans votre base de code locale ont été écrites dans une migration. Cette étape garantit que vous ne manquez pas les modifications légitimes lorsqu’il est temps de générer la migration vide.
2.  Synchroniser avec le contrôle de code source.
3.  Exécutez **Update-Database** pour appliquer toutes les nouvelles migrations archivées par d’autres développeurs.
    **_Remarque :_** *si vous ne recevez aucun avertissement de la commande Update-Database, aucune nouvelle migration n’a été effectuée par les autres développeurs et il n’est pas nécessaire d’effectuer une fusion supplémentaire.*
4.  Exécutez **Add-migration &lt; pick \_ a \_ name &gt; – IgnoreChanges** (par exemple, **Add-migration Merge – IgnoreChanges**). Cela génère une migration avec toutes les métadonnées (y compris un instantané du modèle actuel), mais ignore toutes les modifications détectées lors de la comparaison du modèle actuel à l’instantané dans les dernières migrations (ce qui signifie que vous recevez une méthode de **mise** en forme et de **réduction** de l’espace).
5.  Exécutez **Update-Database** pour réappliquer la dernière migration avec les métadonnées mises à jour.
6.  Poursuivez le développement ou envoyez-le au contrôle de code source (après avoir exécuté vos tests unitaires bien sûr).

Voici l’état de base du \# code local du développeur 2 après l’utilisation de cette approche.

![Migration de fusion](~/ef6/media/mergemigration.png)

### <a name="option-2-update-the-model-snapshot-in-the-last-migration"></a>Option 2 : mettre à jour l’instantané de modèle dans la dernière migration

Cette option est très similaire à l’option 1, mais elle supprime la migration vide supplémentaire, car nous allons l’affronter, qui souhaite des fichiers de code supplémentaires dans leur solution.

**Cette approche est uniquement possible si la dernière migration existe uniquement dans votre base de code locale et n’a pas encore été soumise au contrôle de code source (par exemple, si la dernière migration a été générée par l’utilisateur qui exécute la fusion)**. La modification des métadonnées des migrations que d’autres développeurs ont déjà appliquées à leur base de données de développement, ou même pire encore, peut entraîner des effets secondaires inattendus. Pendant le processus, nous allons restaurer la dernière migration dans notre base de données locale et la réappliquer avec les métadonnées mises à jour.

Alors que la dernière migration doit se trouver dans la base de code locale, il n’existe aucune restriction quant au nombre ou à l’ordre des migrations qui ont lieu. Il peut y avoir plusieurs migrations à partir de plusieurs développeurs différents et les mêmes étapes s’appliquent. nous avons juste étudié deux pour rester simple.

Le processus suivant peut être utilisé pour cette approche, à partir du moment où vous réalisez que vous réalisez des modifications qui doivent être synchronisées à partir du contrôle de code source.

1.  Vérifiez que toutes les modifications de modèle en attente dans votre base de code locale ont été écrites dans une migration. Cette étape garantit que vous ne manquez pas les modifications légitimes lorsqu’il est temps de générer la migration vide.
2.  Synchronisez avec le contrôle de code source.
3.  Exécutez **Update-Database** pour appliquer toutes les nouvelles migrations archivées par d’autres développeurs.
    **_Remarque :_** *si vous ne recevez aucun avertissement de la commande Update-Database, aucune nouvelle migration n’a été effectuée par les autres développeurs et il n’est pas nécessaire d’effectuer une fusion supplémentaire.*
4.  Exécutez **Update-Database – TargetMigration &lt; second \_ Last \_ dernière &gt; migration** (dans l’exemple, nous avons effectué la **mise à jour-Database – TargetMigration AddRating**). Cela restaure la base de données à l’état de la deuxième dernière migration, en « désappliquant » la dernière migration de la base de données.
    **_Remarque :_** *cette étape est requise pour permettre la modification des métadonnées de la migration, car les métadonnées sont également stockées dans le \_ \_ MigrationsHistoryTable de la base de données. C’est pourquoi vous ne devez utiliser cette option que si la dernière migration est uniquement dans votre base de code locale. Si la dernière migration a été appliquée à d’autres bases de données, vous devez également les restaurer et réappliquer la dernière migration pour mettre à jour les métadonnées.* 
5.  Exécutez **Add-migration &lt; Full \_ name \_ , y compris l' \_ horodateur \_ de la \_ dernière \_ migration** &gt; (dans notre exemple, nous avons effectué cette opération, comme **Add-migration 201311062215252 \_ AddReaders**).
    **_Remarque :_** *vous devez inclure l’horodateur afin que les migrations sachent que vous souhaitez modifier la migration existante plutôt que d’en créer une nouvelle.*
    Cela permet de mettre à jour les métadonnées de la dernière migration pour correspondre au modèle actuel. Une fois la commande terminée, vous obtenez l’avertissement suivant, mais c’est exactement ce que vous voulez. «*Seul le code du concepteur pour la migration «201311062215252 \_ AddReaders » a été recréé. Pour regénérer la structure de l’ensemble de la migration, utilisez le paramètre-force.*
6.  Exécutez **Update-Database** pour réappliquer la dernière migration avec les métadonnées mises à jour.
7.  Poursuivez le développement ou envoyez-le au contrôle de code source (après avoir exécuté vos tests unitaires bien sûr).

Voici l’état de base du \# code local du développeur 2 après l’utilisation de cette approche.

![Métadonnées mises à jour](~/ef6/media/updatedmetadata.png)

## <a name="summary"></a>Résumé

L’utilisation de Migrations Code First dans un environnement d’équipe présente des difficultés. Toutefois, une compréhension de base du fonctionnement des migrations et de quelques approches simples pour résoudre les conflits de fusion facilite la résolution de ces problèmes.

Le problème fondamental est lié à des métadonnées incorrectes stockées dans la dernière migration. De ce fait, Code First détecte de manière incorrecte que le schéma actuel et le schéma de base de données ne correspondent pas et pour générer un code incorrect dans la prochaine migration. Cette situation peut être résolue en générant une migration vierge avec le bon modèle ou en mettant à jour les métadonnées dans la dernière migration.
