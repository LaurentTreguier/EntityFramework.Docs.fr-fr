---
title: Prise en main de WPF-EF Core
description: Didacticiel de mise en route pour l’utilisation de WPF avec Entity Framework Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: f183064fafbe2d0e7b8dbdafa921169afc9ffe78
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429921"
---
# <a name="getting-started-with-wpf"></a>Bien démarrer avec WPF

Cette procédure pas à pas montre comment lier des types POCO à des contrôles WPF dans un formulaire « principal-détail ». L’application utilise les API Entity Framework pour remplir les objets avec les données de la base de données, effectuer le suivi des modifications et conserver les données dans la base de données.

Le modèle définit deux types qui participent à une relation un-à-plusieurs : **Category** ( \\ principal principal) et **Product** (détail dépendante \\ ). L’infrastructure de liaison de données WPF permet de naviguer entre les objets connexes : la sélection de lignes dans le mode maître entraîne la mise à jour de la vue détaillée avec les données enfants correspondantes.

Les captures d’écran et les listes de code de cette procédure pas à pas sont extraites de Visual Studio 2019 16.6.5.

> [!TIP]
> Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).

## <a name="pre-requisites"></a>Prérequis

Vous devez avoir installé Visual Studio 2019 16,3 ou version ultérieure avec la **charge de travail de bureau .net** sélectionnée pour effectuer cette procédure pas à pas. Pour plus d’informations sur l’installation de la dernière version de Visual Studio, consultez [installer Visual Studio](/visualstudio/install/install-visual-studio).

## <a name="create-the-application"></a>Création de l’application

1. Ouvrez Visual Studio.
2. Dans la fenêtre de démarrage, choisissez **Créer un projet**.
3. Recherchez « WPF », choisissez **application WPF (.net Core)** , puis choisissez **suivant**.
4. Dans l’écran suivant, donnez un nom au projet, par exemple, **GetStartedWPF** , puis choisissez **créer.**

## <a name="install-the-entity-framework-nuget-packages"></a>Installer les packages NuGet Entity Framework

1. Cliquez avec le bouton droit sur la solution et choisissez **gérer les packages NuGet pour la solution...**

    ![Gérer les packages NuGet](_static/wpf-tutorial-nuget.jpg)

1. Tapez `entityframeworkcore.sqlite` dans la zone de recherche.
1. Sélectionnez le package **Microsoft. EntityFrameworkCore. sqlite** .
1. Vérifiez le projet dans le volet droit, puis cliquez sur **installer** .

    ![Package SQLite](_static/wpf-tutorial-sqlite.jpg)

1. Répétez les étapes pour rechercher `entityframeworkcore.proxies` et installer **Microsoft. EntityFrameworkCore. proxies**.

> [!NOTE]
> Lorsque vous avez installé le package SQLite, il a automatiquement retiré le package de base **Microsoft. EntityFrameworkCore** associé. Le package **Microsoft. EntityFrameworkCore. proxies** prend en charge les données de « chargement différé ». Cela signifie que lorsque vous avez des entités avec des entités enfants, seuls les parents sont extraits lors de la charge initiale. Les proxies détectent quand une tentative d’accès aux entités enfants est effectuée et les charge automatiquement à la demande.

## <a name="define-a-model"></a>Définir un modèle

Dans cette procédure pas à pas, vous allez implémenter un modèle à l’aide de « code First ». Cela signifie que EF Core créera les tables et le schéma de base de données en fonction des classes C# que vous définissez.

Ajoutez une nouvelle classe. Donnez-lui le nom : `Product.cs` et renseignez-le comme suit :

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

Ensuite, ajoutez une classe nommée `Category.cs` et remplissez-la avec le code suivant :

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation. Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.

En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; . Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.

Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.

Ajoutez une nouvelle `ProductContext.cs` classe au projet avec la définition suivante :

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet`Informe EF Core les entités C# qui doivent être mappées à la base de données.
* Il existe plusieurs façons de configurer le EF Core `DbContext` . Vous pouvez en savoir plus à ce sujet dans : [configuration d’un DbContext](xref:core/dbcontext-configuration/index).
* Cet exemple utilise le `OnConfiguring` remplacement pour spécifier un fichier de données SQLite.
* L' `UseLazyLoadingProxies` appel indique à EF Core d’implémenter le chargement différé, de sorte que les entités enfants sont chargées automatiquement lorsqu’elles sont accessibles à partir du parent.

Appuyez sur **Ctrl + Maj + B** ou accédez à **générer générer la &gt; solution** pour compiler le projet.

> [!TIP]
> Découvrez les différences qui consistaient à maintenir la synchronisation de votre base de données et de vos modèles de EF Core : la [gestion des schémas de base de données](xref:core/managing-schemas/index).

## <a name="lazy-loading"></a>Chargement différé

La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation. Dans Entity Framework Core, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.

EF Core vous donne la possibilité de charger automatiquement des entités associées à partir de la base de données la première fois que vous accédez à la propriété de navigation. Avec ce type de chargement (appelé chargement différé), sachez que la première fois que vous accédez à chaque propriété de navigation, une requête distincte est exécutée sur la base de données si le contenu n’est pas déjà dans le contexte.

Lors de l’utilisation des types d’entités POCO (Plain Old C# Object), EF Core réalise un chargement différé en créant des instances de types de proxy dérivés au cours de l’exécution, puis en substituant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement. Pour bénéficier du chargement différé d’objets connexes, vous devez déclarer les accesseurs get de propriété de navigation comme **public** et **virtuel** ( **substituable** dans Visual Basic) et votre classe ne doit pas être **sealed** ( **NotOverridable** dans Visual Basic). Lorsque vous utilisez Database First, les propriétés de navigation sont automatiquement configurées pour permettre le chargement différé.

## <a name="bind-object-to-controls"></a>Lier un objet à des contrôles

Ajoutez les classes définies dans le modèle en tant que sources de données pour cette application WPF.

1. Double-cliquez sur **MainWindow. Xaml** dans Explorateur de solutions pour ouvrir le formulaire principal
1. Choisissez l’onglet **XAML** pour modifier le code XAML.
1. Immédiatement après la `Window` balise d’ouverture, ajoutez les sources suivantes pour vous connecter aux entités EF Core.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. Cela configure la source pour les catégories « parent » et la deuxième source pour les produits « Detail ».
1. Ensuite, ajoutez le balisage suivant à votre code XAML après la `Window.Resources` balise de fermeture.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. Notez que `CategoryId` a la valeur `ReadOnly` , car elle est affectée par la base de données et ne peut pas être modifiée.

## <a name="adding-a-details-grid"></a>Ajout d’une grille de détails

Maintenant que la grille existe pour afficher les catégories, la grille détails peut être ajoutée pour afficher les produits.

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

Enfin, ajoutez un `Save` bouton et un fil dans l’événement Click à `Button_Click` .

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

Votre mode Design doit ressembler à ceci :

![Capture d’écran du Concepteur WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>Ajouter du code qui gère l’interaction des données

Il est temps d’ajouter des gestionnaires d’événements à la fenêtre principale.

1. Dans la fenêtre XAML, cliquez sur l’élément de **&lt; fenêtre &gt;** pour sélectionner la fenêtre principale.
1. Dans la fenêtre **Propriétés** , choisissez **événements** en haut à droite, puis double-cliquez sur la zone de texte à droite de l’étiquette **chargée** .

    ![Propriétés de la fenêtre principale](_static/wpf-tutorial-loaded.jpg)

Cela vous amène au code-behind pour le formulaire, nous allons maintenant modifier le code pour utiliser le `ProductContext` pour effectuer l’accès aux données. Mettez à jour le code comme indiqué ci-dessous.

Le code déclare une instance de longue durée de `ProductContext` . L' `ProductContext` objet est utilisé pour interroger et enregistrer des données dans la base de données. La `Dispose()` méthode sur l' `ProductContext` instance est ensuite appelée à partir de la `OnClosing` méthode substituée. Les commentaires de code expliquent ce que fait chaque étape.

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> Le code utilise un appel à `EnsureCreated()` pour générer la base de données lors de la première exécution. Cela est acceptable pour les démonstrations, mais dans les applications de production, vous devez examiner les [migrations](xref:core/managing-schemas/migrations/index) pour gérer votre schéma. Le code s’exécute également de façon synchrone, car il utilise une base de données SQLite locale. Pour les scénarios de production qui impliquent généralement un serveur distant, envisagez d’utiliser les versions asynchrones des `Load` `SaveChanges` méthodes et.

## <a name="test-the-wpf-application"></a>Tester l’application WPF

Compilez et exécutez l’application en appuyant sur **F5** ou en choisissant **Déboguer &gt; Démarrer le débogage**. La base de données doit être créée automatiquement avec un fichier nommé `products.db` . Entrez un nom de catégorie et appuyez sur entrée, puis ajoutez des produits à la grille inférieure. Cliquez sur enregistrer et observez l’actualisation de la grille avec les ID fournis par la base de données. Mettez en surbrillance une ligne et appuyez sur **supprimer** pour supprimer la ligne. L’entité sera supprimée lorsque vous cliquerez sur **Enregistrer**.

![Application en cours d’exécution](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>Notification de modification de propriété

Cet exemple s’appuie sur quatre étapes pour synchroniser les entités avec l’interface utilisateur.

1. L’appel initial `_context.Categories.Load()` charge les données de catégories.
1. Les proxys de chargement différé chargent les données des produits dépendants.
1. Le suivi des modifications intégré de EF Core apporte les modifications nécessaires aux entités, y compris les insertions et les suppressions, lorsque `_context.SaveChanges()` est appelé.
1. Appelle pour `DataGridView.Items.Refresh()` forcer un rechargement avec les ID nouvellement générés.

Cela fonctionne pour notre exemple de prise en main, mais vous pouvez avoir besoin de code supplémentaire pour d’autres scénarios. Les contrôles WPF affichent l’interface utilisateur en lisant les champs et les propriétés sur vos entités. Lorsque vous modifiez une valeur dans l’interface utilisateur (IU), cette valeur est transmise à votre entité. Lorsque vous modifiez la valeur d’une propriété directement sur votre entité, telle que son chargement à partir de la base de données, WPF ne reflète pas immédiatement les modifications apportées à l’interface utilisateur. Le moteur de rendu doit être averti des modifications. Le projet a fait cela en appelant manuellement `Refresh()` . Une façon simple d’automatiser cette notification consiste à implémenter l’interface [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) . Les composants WPF détectent automatiquement l’interface et s’inscrivent pour les événements de modification. L’entité est chargée de déclencher ces événements.

> [!TIP]
> Pour en savoir plus sur la gestion des modifications, consultez [comment implémenter la notification de modification de propriété](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [la configuration d’un DbContext](xref:core/dbcontext-configuration/index).
