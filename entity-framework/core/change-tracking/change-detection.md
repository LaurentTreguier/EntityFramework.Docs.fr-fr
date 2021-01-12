---
title: Détection des modifications et notifications-EF Core
description: Détection des modifications des propriétés et des relations à l’aide de DetectChanges ou de notifications
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129733"
---
# <a name="change-detection-and-notifications"></a>Détection des modifications et notifications

Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités. Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé. Cela est abordé dans [change Tracking dans EF Core](xref:core/change-tracking/index), et ce document suppose que les États d’entité et les principes de base du suivi des modifications Entity Framework Core (EF Core) sont compris.

Les modifications de propriété et de relation de suivi requièrent que DbContext soit en mesure de détecter ces modifications. Ce document explique comment cette détection se produit, et comment utiliser les notifications de propriétés ou les proxys de suivi des modifications pour forcer la détection immédiate des modifications.

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).

## <a name="snapshot-change-tracking"></a>Suivi des modifications par instantané

Par défaut, EF Core crée un instantané des valeurs de propriété de chaque entité lorsqu’il est d’abord suivi par une instance DbContext. Les valeurs stockées dans cet instantané sont ensuite comparées aux valeurs actuelles de l’entité afin de déterminer les valeurs de propriété qui ont changé.

Cette détection des modifications se produit lorsque SaveChanges est appelé pour s’assurer que toutes les valeurs modifiées sont détectées avant d’envoyer des mises à jour à la base de données. Toutefois, la détection des modifications intervient également à d’autres moments pour s’assurer que l’application fonctionne avec des informations de suivi à jour. La détection des modifications peut être forcée à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="when-change-detection-is-needed"></a>Quand la détection des modifications est nécessaire

La détection des modifications est nécessaire quand une propriété ou une navigation a été modifiée _sans utiliser EF Core pour effectuer cette modification_. Par exemple, envisagez de charger des blogs et des billets, puis d’apporter des modifications à ces entités :

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

Si vous examinez l' [affichage de débogage du dispositif de suivi](xref:core/change-tracking/debug-views) des modifications avant d’appeler, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> cela indique que les modifications apportées n’ont pas été détectées et ne sont donc pas reflétées dans les États d’entité et les données de propriété modifiées

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Plus précisément, l’état de l’entrée de blog est toujours `Unchanged` , et la nouvelle publication n’apparaît pas en tant qu’entité suivie. (Le astucieux notera que les propriétés signalent leurs nouvelles valeurs, même si ces modifications n’ont pas encore été détectées par EF Core. Cela est dû au fait que la vue de débogage lit les valeurs actuelles directement à partir de l’instance d’entité.)

Comparez ceci avec la vue Debug après avoir appelé DetectChanges :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

À présent, le blog est correctement marqué comme `Modified` et le nouveau billet a été détecté et suivi en tant que `Added` .

Au début de cette section, nous avons indiqué que la détection des modifications est nécessaire lors de l’utilisation _de EF Core pour effectuer la modification_. C’est ce qui se passe dans le code ci-dessus. Autrement dit, les modifications apportées à la propriété et à la navigation sont effectuées _directement sur les instances d’entité_, et non à l’aide des méthodes de EF Core.

Comparez ceci au code suivant qui modifie les entités de la même façon, mais cette fois à l’aide de EF Core méthodes :

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

Dans ce cas, la vue de débogage change tracker indique que tous les États d’entité et les modifications de propriété sont connus, même si la détection de modifications n’a pas été effectuée. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType>En effet, est une méthode EF Core, ce qui signifie que EF Core connaît immédiatement la modification apportée par cette méthode. De même, <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> l’appel de permet EF Core de connaître immédiatement la nouvelle entité et de la suivre de manière appropriée.

> [!TIP]
> N’essayez pas de détecter les modifications en utilisant toujours EF Core méthodes pour apporter des modifications d’entité. Cela est souvent plus fastidieux et fonctionne moins bien que d’apporter des modifications aux entités de manière normale. Ce document a pour but de vous informer de la détection des modifications et du moment où elles sont nécessaires. L’objectif n’est pas de favoriser l’évitement de la détection des modifications.

### <a name="methods-that-automatically-detect-changes"></a>Méthodes qui détectent automatiquement les modifications

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est appelé automatiquement par les méthodes dans lesquelles cela risque d’avoir un impact sur les résultats. Ces méthodes sont les suivantes :

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , pour vous assurer que toutes les modifications sont détectées avant de mettre à jour la base de données.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , pour garantir que les États d’entité et les propriétés modifiées sont à jour.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, pour s’assurer que le résultat est exact.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, pour garantir des États d’entité corrects pour les entités principal/parent avant le en cascade.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>pour vous assurer que le graphique suivi est à jour.

Il existe également certains emplacements où la détection des modifications se produit sur une seule instance d’entité, plutôt que sur l’ensemble du graphique des entités suivies. Ces emplacements sont les suivants :

- Lorsque <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> vous utilisez, pour vous assurer que l’état de l’entité et les propriétés modifiées sont à jour.
- Lorsque <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> vous utilisez des méthodes telles que `Property` , `Collection` `Reference` ou `Member` pour vous assurer que les modifications de propriété, les valeurs actuelles, etc. sont à jour.
- Lorsqu’une entité dépendante/enfant va être supprimée parce qu’une relation requise a été interrompue. Cela détecte quand une entité ne doit pas être supprimée car elle a été reapparentée.

La détection locale des modifications pour une seule entité peut être déclenchée explicitement en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .

> [!NOTE]
> Les modifications de détection locale peuvent manquer certaines modifications qu’une détection complète trouverait. Cela se produit lorsque des actions en cascade résultant de modifications non détectées d’autres entités ont un impact sur l’entité en question. Dans ce cas, l’application peut avoir besoin de forcer une analyse complète de toutes les entités en appelant explicitement <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="disabling-automatic-change-detection"></a>Désactivation de la détection automatique des modifications

Les performances de la détection des modifications ne sont pas un goulet d’étranglement pour la plupart des applications. Toutefois, la détection des modifications peut devenir un problème de performances pour certaines applications qui effectuent le suivi de milliers d’entités. (Le nombre exact dépend de nombreux éléments, tels que le nombre de propriétés de l’entité.) Pour cette raison, la détection automatique des modifications peut être désactivée à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> . Par exemple, envisagez de traiter les entités de jointure dans une relation plusieurs-à-plusieurs avec des charges utiles :

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

Comme nous le savons dans la section précédente, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> détectent automatiquement les modifications. Toutefois, après avoir appelé des entrées, le code n’effectue pas de modification de l’état d’une entité ou d’une propriété. (La définition de valeurs de propriétés normales sur des entités ajoutées n’entraîne pas de modification de l’État.) Par conséquent, le code désactive la détection automatique des modifications inutile lors de l’appel de la méthode de base SaveChanges. Le code utilise également un bloc try/finally pour s’assurer que le paramètre par défaut est restauré même si SaveChanges échoue.

> [!TIP]
> Ne partez pas du principe que votre code doit désactiver la détection automatique des modifications à pour s’exécuter correctement. Cela est nécessaire uniquement pour le profilage d’une application qui effectue le suivi de nombreuses entités indique que les performances de la détection des modifications sont un problème.

### <a name="detecting-changes-and-value-conversions"></a>Détection des modifications et des conversions de valeurs

Pour utiliser le suivi des modifications d’instantané avec un type d’entité, EF Core devez être en mesure d’effectuer les opérations suivantes :

- Créer un instantané de chaque valeur de propriété lorsque l’entité est suivie
- Comparez cette valeur à la valeur actuelle de la propriété.
- Générer un code de hachage pour la valeur

Cela est géré automatiquement par EF Core pour les types qui peuvent être mappés directement à la base de données. Toutefois, lorsqu’un [convertisseur de valeurs est utilisé pour mapper une propriété](xref:core/modeling/value-conversions), ce convertisseur doit spécifier comment effectuer ces actions. Cela est accompli avec un comparateur de valeur et est décrit en détail dans la documentation sur les [comparateurs de valeurs](xref:core/modeling/value-comparers) .

## <a name="notification-entities"></a>Entités de notification

Le suivi des modifications d’instantané est recommandé pour la plupart des applications. Toutefois, les applications qui effectuent le suivi de nombreuses entités et/ou apportent de nombreuses modifications à ces entités peuvent tirer parti de l’implémentation d’entités qui notifient automatiquement EF Core lorsque leurs valeurs de propriété et de navigation changent. Ils sont appelés « entités de notification ».

### <a name="implementing-notification-entities"></a>Implémentation des entités de notification

Les entités de notification utilisent les <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfaces et, qui font partie de la bibliothèque de classes de base .net (BCL). Ces interfaces définissent les événements qui doivent être déclenchés avant et après la modification d’une valeur de propriété. Exemple :

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

En outre, les navigations dans les collections doivent implémenter `INotifyCollectionChanged` ; dans l’exemple ci-dessus, cela satisfait à l’aide d’un <xref:System.Collections.ObjectModel.ObservableCollection%601> de publications. EF Core est également fourni avec une <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implémentation qui offre des recherches plus efficaces au détriment du classement stable.

La majeure partie de ce code de notification est généralement déplacée dans une classe de base non mappée. Exemple :

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>Configuration des entités de notification

Il n’existe aucun moyen pour EF Core de valider que `INotifyPropertyChanging` ou `INotifyPropertyChanged` sont entièrement implémentés pour une utilisation avec EF Core. En particulier, certaines utilisations de ces interfaces le font avec des notifications uniquement sur certaines propriétés, plutôt que sur toutes les propriétés (y compris les navigations) requises par EF Core. C’est la raison pour laquelle EF Core ne se raccorde pas automatiquement à ces événements.

Au lieu de cela, EF Core doit être configuré pour utiliser ces entités de notification. Cette opération est généralement effectuée pour tous les types d’entité en appelant <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Exemple :

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

(La stratégie peut également être définie différemment pour les différents types d’entité à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> , mais cela est généralement productif, car DetectChanges est toujours requis pour ces types qui ne sont pas des entités de notification.)

Le suivi des modifications de notification complet requiert que `INotifyPropertyChanging` et `INotifyPropertyChanged` soient implémentés. Cela permet d’enregistrer les valeurs d’origine juste avant la modification de la valeur de propriété, ce qui évite d’avoir à EF Core créer un instantané lors du suivi de l’entité. Les types d’entités qui implémentent uniquement `INotifyPropertyChanged` peuvent également être utilisés avec EF Core. Dans ce cas, EF crée toujours un instantané lors du suivi d’une entité pour effectuer le suivi des valeurs d’origine, mais utilise ensuite les notifications pour détecter les modifications immédiatement, plutôt que d’avoir à appeler DetectChanges.

Les différentes <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valeurs sont résumées dans le tableau suivant.

| ChangeTrackingStrategy                              | Interfaces nécessaires                                      | Nécessite DetectChanges | Instantanés des valeurs d’origine
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| Instantané                                            | Aucun                                                   | Oui                 | Oui
| ChangedNotifications                                | Interface INotifyPropertyChanged                                 | Non                  | Oui
| ChangingAndChangedNotifications                     | INotifyPropertyChanged et INotifyPropertyChanging     | Non                  | Non
| ChangingAndChangedNotificationsWithOriginalValues   | INotifyPropertyChanged et INotifyPropertyChanging     | Non                  | Oui

### <a name="using-notification-entities"></a>Utilisation des entités de notification

Les entités de notification se comportent comme n’importe quelle autre entité, sauf que les modifications apportées aux instances d’entité ne nécessitent pas d’appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> pour détecter ces modifications. Exemple :

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

Avec les entités normales, l' [affichage de débogage du suivi des modifications](xref:core/change-tracking/debug-views) a montré que ces modifications n’ont pas été détectées tant que DetectChanges n’a pas été appelé. Si vous examinez l’affichage débogage lorsque les entités de notification sont utilisées, cela indique que ces modifications ont été détectées immédiatement :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>Proxys de suivi des modifications

> [!NOTE]
> Les proxys de suivi des modifications ont été introduits dans EF Core 5,0.

EF Core pouvez générer de manière dynamique des types de proxy qui implémentent <xref:System.ComponentModel.INotifyPropertyChanging> et <xref:System.ComponentModel.INotifyPropertyChanged> . Pour cela, vous devez installer le package NuGet [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) et activer les proxys de suivi des modifications avec, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

La création d’un proxy dynamique implique la création d’un nouveau type .NET dynamique (à l’aide de l’implémentation des proxies [Castle. Core](https://www.nuget.org/packages/Castle.Core/) ), qui hérite du type d’entité, puis remplace tous les accesseurs set de propriété. Par conséquent, les types d’entités pour les proxies doivent être des types qui peuvent être hérités de et doivent avoir des propriétés qui peuvent être substituées. En outre, les navigations de collection créées explicitement doivent implémenter l' <xref:System.Collections.Specialized.INotifyCollectionChanged> exemple suivant :

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

L’un des inconvénients majeurs des proxys de suivi des modifications est que EF Core devez toujours suivre les instances du proxy, et jamais les instances du type d’entité sous-jacent. Cela est dû au fait que les instances du type d’entité sous-jacent ne génèrent pas de notifications, ce qui signifie que les modifications apportées à ces entités ne seront pas exécutées.

EF Core crée automatiquement des instances de proxy lors de l’interrogation de la base de données, cet inconvénient est généralement limité au suivi de nouvelles instances d’entité. Ces instances doivent être créées à l’aide des <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> méthodes d’extension, et **pas** normalement à l’aide de `new` . Cela signifie que le code des exemples précédents doit maintenant utiliser `CreateProxy` :

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>Événements de suivi des modifications

EF Core déclenche l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> événement lorsqu’une entité est suivie pour la première fois. Les futures modifications de l’état de l’entité entraînent des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> événements. Pour plus d’informations, consultez [événements .net dans EF Core](xref:core/logging-events-diagnostics/events) .

> [!NOTE]
> L' `StateChanged` événement n’est pas déclenché lorsqu’une entité est suivie pour la première fois, même si l’État est passé de `Detached` à l’un des autres États. Veillez à écouter les `StateChanged` événements et `Tracked` pour obtenir toutes les notifications pertinentes.
