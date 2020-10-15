---
title: Entités de suivi automatique - EF6
description: Entité d’auto-suivi dans Entity Framework 6.
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/index
ms.openlocfilehash: 56bcd79d0c9949b9ccc06efe4dbc5a36bb88b158
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065548"
---
# <a name="self-tracking-entities"></a>Entités de suivi automatique

> [!IMPORTANT]
> Nous ne recommandons plus d’utiliser le modèle des entités de suivi automatique. Il reste disponible uniquement pour prendre en charge les applications existantes. Si votre application doit utiliser des graphiques d’entités déconnectés, envisagez d’autres alternatives comme les [Entités traçables](https://trackableentities.github.io/), qui présentent une technologie similaire aux entités de suivi automatique, mais développée de manière plus active par la communauté, ou bien l’écriture de code personnalisé à l’aide des API de suivi de changements de bas niveau.

Dans une application Entity Framework, c’est un contexte qui est responsable du suivi des changements dans vos objets. Vous utilisez la méthode SaveChanges pour conserver les changements dans la base de données. Quand vous utilisez des applications multicouches, les objets d’entité sont généralement déconnectés du contexte et vous devez choisir à la fois comment suivre les changements et comment les signaler au contexte. Les entités de suivi automatique peuvent vous aider à suivre les changements dans n’importe quelle couche, puis de replacer ces changements dans un contexte pour les enregistrer.  

Utilisez les entités de suivi automatique uniquement si le contexte n’est pas disponible sur la couche où ont lieu les changements du graphique d’objet. Si le contexte est disponible, il s’occupe de suivre les changements, vous n’avez donc pas besoin d’utiliser les entités de suivi automatique.  

Cet élément de modèle génère deux fichiers .tt (modèle de texte) :  

- Le fichier **\<model name\>.tt** génère les types d’entités et une classe d’assistance contenant la logique de suivi des modifications appliquée par les entités d’auto-suivi et les méthodes d’extension qui permettent de définir l’état de ces entités.  
- Le fichier **\<model name\>.Context.tt** génère un contexte dérivé et une classe d’extension contenant les méthodes **ApplyChanges** des classes **ObjectContext** et **ObjectSet**. Ces méthodes examinent les informations de suivi des modifications contenues dans le graphique des entités de suivi automatique pour déduire l'ensemble des opérations à effectuer pour enregistrer les modifications dans la base de données.  

## <a name="get-started"></a>Découvrir  

Pour commencer, visitez la page [Procédure pas à pas des entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough).  

## <a name="functional-considerations-when-working-with-self-tracking-entities"></a>Points fonctionnels à prendre en considération lors de l’utilisation d’entités de suivi automatique  
> [!IMPORTANT]
> Nous ne recommandons plus d’utiliser le modèle des entités de suivi automatique. Il reste disponible uniquement pour prendre en charge les applications existantes. Si votre application doit utiliser des graphiques d’entités déconnectés, envisagez d’autres alternatives comme les [Entités traçables](https://trackableentities.github.io/), qui présentent une technologie similaire aux entités de suivi automatique, mais développée de manière plus active par la communauté, ou bien l’écriture de code personnalisé à l’aide des API de suivi de changements de bas niveau.

Tenez compte des éléments suivants lors de l'utilisation d'entités de suivi automatique :  

- Assurez-vous que votre projet client possède une référence à l'assembly contenant les types d'entités. Si vous ajoutez uniquement la référence de service au projet client, celui-ci utilisera les types de proxy WCF et non les types réels d'entité de suivi automatique. Cela signifie que vous n'aurez pas accès aux fonctionnalités de notification automatisée qui gèrent le suivi des entités sur client. Si vous ne souhaitez pas inclure les types d'entités, vous devrez définir manuellement les informations de suivi des modifications sur le client pour les modifications à renvoyer au service.  
- Les appels aux opérations de service doivent être sans état et créer une nouvelle instance du contexte de l'objet. Nous vous recommandons aussi de créer un contexte d’objet dans un bloc **using**.  
- Quand vous envoyez le graphique modifié sur le client au service et que vous voulez continuer à utiliser le même graphique sur le client, vous devez effectuer une itération manuelle au sein du graphique et appeler la méthode **AcceptChanges** sur chaque objet pour réinitialiser le suivi des changements.  

    > Si des objets dans votre graphique contiennent des propriétés avec des valeurs générées par la base de données (par exemple, des valeurs d’identité ou de concurrence), l’Entity Framework remplace les valeurs de ces propriétés par les valeurs générées par la base de données après l’appel de la méthode **SaveChanges**. Vous pouvez implémenter votre opération de service pour retourner des objets enregistrés ou une liste de valeurs de propriété générées pour les objets au client. Le client devra ensuite remplacer les instances d'objet ou valeurs de propriété d'objet par les objets ou valeurs de propriété retournés à partir de l'opération de service.  
- La fusion de graphiques à partir de plusieurs demandes de service peut introduire des objets avec des valeurs de clés dupliquées dans le graphique résultant. L’Entity Framework ne supprime pas les objets avec des clés en double quand vous appelez la méthode **ApplyChanges**, mais lève une exception. Pour éviter les graphiques avec des valeurs de clé en double, suivez l’un des modèles décrits dans le blog suivant : [Entités de suivi automatique : ApplyChanges et entités en double](https://go.microsoft.com/fwlink/?LinkID=205119&clcid=0x409).  
- Lorsque vous modifiez la relation entre les objets en définissant la propriété de clé étrangère, la propriété de navigation de référence a la valeur Null et n'est pas synchronisée sur l'entité principale appropriée sur le client. Dès que le graphique est attaché au contexte d’objet (par exemple, une fois que vous avez appelé la méthode **ApplyChanges**), les propriétés de clé étrangère et les propriétés de navigation sont synchronisées.  

    > Le fait que la propriété de navigation de référence ne soit pas synchronisée avec l'objet principal approprié peut poser un problème si vous avez spécifié la suppression en cascade dans la relation de clé étrangère. Si vous supprimez l'objet principal, la suppression ne sera pas propagée aux objets dépendants. Si vous avez spécifié des suppressions en cascade, utilisez les propriétés de navigation pour modifier les relations au lieu de définir la propriété de clé étrangère.  
- Les entités de suivi automatique ne sont pas activées pour effectuer un chargement différé.  
- La sérialisation binaire et la sérialisation en objets de gestion d’état ASP.NET ne sont pas prises en charge par les entités de suivi automatique. Toutefois, vous pouvez personnaliser le modèle pour ajouter la prise en charge de la sérialisation binaire. Pour plus d’informations, consultez [Utilisation de la sérialisation binaire et de ViewState avec les entités de suivi automatique](https://go.microsoft.com/fwlink/?LinkId=199208).  

## <a name="security-considerations"></a>Considérations relatives à la sécurité  

Les considérations de sécurité suivantes doivent être prises en compte quand vous utilisez les entités de suivi automatique :  

- Un service ne doit pas compter sur les requêtes pour récupérer ou mettre à jour les données d'un client non approuvé ou via un canal non approuvé. Un client doit être authentifié : un canal sécurisé doit être utilisé ou bien une enveloppe de message. Les requêtes de clients pour mettre à jour ou récupérer des données doivent être validées pour vérifier qu'elles sont conformes aux modifications attendues et légitimes pour le scénario donné.  
- Évitez d'utiliser des informations sensibles comme clés d'entité (par exemple, des numéros de sécurité sociale). Cela limite l'éventualité de sérialiser par inadvertance des informations sensibles dans les graphiques d'entité de suivi automatique sur un client qui n'est pas d'un niveau de confiance suffisant. Avec les associations indépendantes, la clé d'origine d'une entité associée à celle qui est sérialisée peut être également envoyée au client.  
- Pour éviter la propagation de messages d’exception contenant des données sensibles vers la couche cliente, les appels à **ApplyChanges** et **SaveChanges** sur la couche serveur doivent être wrappés dans le code de gestion des exceptions.  
