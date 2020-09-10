---
title: Testabilité et Entity Framework 4,0-EF6
description: Testabilité et Entity Framework 4,0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: e1ded772bfee4f1870a891eaa525b33da8e9d3bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618357"
---
# <a name="testability-and-entity-framework-40"></a>Testabilité et Entity Framework 4,0
Scott Allen

Date de publication : mai 2010

## <a name="introduction"></a>Introduction

Ce livre blanc décrit et montre comment écrire du code testable avec ADO.NET Entity Framework 4,0 et Visual Studio 2010. Ce document ne tente pas de se concentrer sur une méthodologie de test spécifique, telle que la conception pilotée par test (TDD) ou la conception pilotée par comportement (BDD). Au lieu de cela, cet article se concentrera sur la façon d’écrire du code qui utilise le ADO.NET Entity Framework tout en restant facile à isoler et à tester de manière automatisée. Nous allons examiner les modèles de conception courants qui facilitent les tests dans les scénarios d’accès aux données et comment appliquer ces modèles quand vous utilisez l’infrastructure. Nous examinerons également les fonctionnalités spécifiques de l’infrastructure pour voir comment ces fonctionnalités peuvent fonctionner dans du code testable.

## <a name="what-is-testable-code"></a>Qu’est-ce que le code testable ?

La possibilité de vérifier un logiciel à l’aide de tests unitaires automatisés offre de nombreux avantages souhaitables. Tout le monde sait que les bons tests vont réduire le nombre de défauts logiciels dans une application et augmenter la qualité de l’application, mais que les tests unitaires sont en place bien plus que de trouver des bogues.

Une bonne suite de tests unitaires permet à une équipe de développement de gagner du temps et de garder le contrôle du logiciel qu’elle crée. Une équipe peut apporter des modifications au code existant, Refactoriser, reconcevoir et restructurer des logiciels pour répondre à de nouvelles exigences, et ajouter de nouveaux composants dans une application tout en sachant que la suite de tests peut vérifier le comportement de l’application. Les tests unitaires font partie d’un cycle de commentaires rapide pour faciliter les modifications et préserver la maintenabilité des logiciels à mesure que la complexité augmente.

Toutefois, les tests unitaires sont fournis avec un prix. Une équipe doit consacrer le temps nécessaire à la création et à la maintenance des tests unitaires. L’effort requis pour créer ces tests est directement lié à la **testabilité** du logiciel sous-jacent. Le logiciel est-il facile à tester ? Une équipe qui développe des logiciels avec la testabilité à l’esprit créera des tests efficaces plus rapidement que l’équipe travaillant avec des logiciels non testables.

Microsoft a conçu le ADO.NET Entity Framework 4,0 (EF4) avec la testabilité à l’esprit. Cela ne signifie pas que les développeurs écrivent des tests unitaires sur le code du Framework lui-même. Au lieu de cela, les objectifs de testabilité pour EF4 facilitent la création de code testable qui s’appuie sur l’infrastructure. Avant d’examiner des exemples spécifiques, il est utile de comprendre les qualités du code testable.

### <a name="the-qualities-of-testable-code"></a>Les qualités du code testable

Le code facile à tester présentera toujours au moins deux traits. Tout d’abord, le code testable est facile à **observer**. Étant donné un ensemble d’entrées, il doit être facile d’observer la sortie du code. Par exemple, le test de la méthode suivante est simple, car la méthode retourne directement le résultat d’un calcul.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Le test d’une méthode est difficile si la méthode écrit la valeur calculée dans un socket réseau, une table de base de données ou un fichier comme le code suivant. Le test doit effectuer un travail supplémentaire pour récupérer la valeur.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

Deuxièmement, il est facile d' **isoler**du code testable. Utilisons le pseudo-code suivant comme mauvais exemple de code testable.

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

La méthode est facile à observer : nous pouvons transmettre une stratégie d’assurance et vérifier que la valeur de retour correspond à un résultat attendu. Toutefois, pour tester la méthode, vous devez disposer d’une base de données installée avec le schéma correct, et configurer le serveur SMTP en cas de tentative d’envoi d’un e-mail par la méthode.

Le test unitaire souhaite uniquement vérifier la logique de calcul à l’intérieur de la méthode, mais le test peut échouer parce que le serveur de messagerie est hors connexion ou que le serveur de base de données a été déplacé. Ces deux échecs ne sont pas liés au comportement que le test veut vérifier. Le comportement est difficile à isoler.

Les développeurs de logiciels qui cherchent à écrire du code testable s’efforcent souvent de conserver une séparation des préoccupations dans le code qu’ils écrivent. La méthode ci-dessus doit se concentrer sur les calculs d’entreprise et déléguer les détails d’implémentation de la base de données et du courrier électronique à d’autres composants. Robert C. Martin appelle ce principe de responsabilité unique. Un objet doit encapsuler une seule responsabilité étroite, comme le calcul de la valeur d’une stratégie. Toutes les autres opérations de base de données et de notification doivent être de la responsabilité d’un autre objet. Le code écrit de cette manière est plus facile à isoler, car il est axé sur une seule tâche.

Dans .NET, nous disposons des abstractions dont nous avons besoin pour suivre le principe de responsabilité unique et parvenir à l’isolation. Nous pouvons utiliser des définitions d’interface et forcer le code à utiliser l’abstraction d’interface au lieu d’un type concret. Plus loin dans ce document, nous verrons comment une méthode telle que l’exemple mal présenté ci-dessus peut fonctionner avec des interfaces qui *semblent* communiquer avec la base de données. Toutefois, au moment du test, nous pouvons remplacer une implémentation factice qui ne communique pas avec la base de données, mais qui contient à la place des données en mémoire. Cette implémentation factice isole le code des problèmes non liés dans le code d’accès aux données ou la configuration de la base de données.

L’isolation présente des avantages supplémentaires. L’exécution du calcul de l’activité dans la dernière méthode ne doit prendre que quelques millisecondes, mais le test lui-même peut s’exécuter pendant plusieurs secondes à mesure que le code saute sur le réseau et communique avec différents serveurs. Les tests unitaires doivent s’exécuter rapidement pour faciliter les petites modifications. Les tests unitaires doivent également être reproductibles et ne pas échouer car un composant qui n’est pas lié au test a un problème. L’écriture de code facile à observer et à isoler signifie que les développeurs auront un temps plus facile pour écrire des tests pour le code, passer moins de temps à attendre l’exécution des tests et, plus important encore, passer moins de temps à suivre les bogues qui n’existent pas.

Nous espérons que vous pouvez apprécier les avantages des tests et comprendre les qualités qu’il présente. Nous sommes sur le point de traiter l’écriture de code qui fonctionne avec EF4 pour enregistrer des données dans une base de données tout en restant observables et faciles à isoler, mais tout d’abord, nous allons affiner l’étude des conceptions pouvant être testées pour l’accès aux données.

## <a name="design-patterns-for-data-persistence"></a>Modèles de conception pour la persistance des données

Les deux exemples incorrects présentés précédemment avaient trop de responsabilités. Le premier exemple inapproprié devait effectuer un calcul *et* écrire dans un fichier. Le deuxième exemple incorrect devait lire les données d’une base de données *et* effectuer un calcul d’entreprise *et* envoyer des e-mails. En concevant des méthodes plus petites qui distinguent les préoccupations et délèguent la responsabilité à d’autres composants, vous ferez de superbes progrès pour écrire du code testable. L’objectif est de créer des fonctionnalités en composant des actions à partir d’abstractions petites et concentrées.

Lorsqu’il s’agit de la persistance des données, les petites abstractions concentrées que nous recherchons sont tellement courantes qu’elles ont été documentées en tant que modèles de conception. Les modèles de l’architecture d’applications d’entreprise de Martin Fowler étaient le premier travail à décrire ces modèles à l’impression. Nous fournissons une brève description de ces modèles dans les sections suivantes avant de montrer comment ces ADO.NET Entity Framework implémentent et fonctionnent avec ces modèles.

### <a name="the-repository-pattern"></a>The Repository Pattern

Fowler indique un référentiel qui suit les couches de mappage de données et de domaine à l’aide d’une interface de type collection pour accéder aux objets de domaine. L’objectif du modèle de référentiel est d’isoler le code du détails d’accès aux données, et comme nous l’avons vu, l’isolation précédente est un critère requis pour la testabilité.

La clé de l’isolation est la façon dont le référentiel expose les objets à l’aide d’une interface de type collection. La logique que vous écrivez pour utiliser le référentiel n’a aucune idée de la façon dont le référentiel matérialisera les objets que vous demandez. Le référentiel peut communiquer avec une base de données, ou il peut simplement retourner des objets à partir d’une collection en mémoire. Tout votre code doit savoir que le référentiel semble gérer la collection et que vous pouvez récupérer, ajouter et supprimer des objets de la collection.

Dans les applications .NET existantes, un référentiel concret hérite souvent d’une interface générique comme suit :

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Nous allons apporter quelques modifications à la définition de l’interface lorsque nous fournissons une implémentation pour EF4, mais le concept de base reste le même. Le code peut utiliser un référentiel concret qui implémente cette interface pour récupérer une entité par sa valeur de clé primaire, pour récupérer une collection d’entités en fonction de l’évaluation d’un prédicat, ou simplement récupérer toutes les entités disponibles. Le code peut également ajouter et supprimer des entités par le biais de l’interface de référentiel.

Étant donné un IRepository d’objets Employee, le code peut effectuer les opérations suivantes.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Étant donné que le code utilise une interface (IRepository of Employee), nous pouvons fournir le code avec différentes implémentations de l’interface. Une implémentation peut être une implémentation sauvegardée par EF4 et la persistance d’objets dans une base de données Microsoft SQL Server. Une implémentation différente (celle que nous utilisons pendant le test) peut être sauvegardée par une liste en mémoire d’objets Employee. L’interface permet d’obtenir un isolement dans le code.

Notez que l' &lt; interface IRepository t &gt; n’expose pas d’opération d’enregistrement. Comment mettre à jour les objets existants ? Vous pouvez vous trouver parmi les définitions de IRepository qui incluent l’opération d’enregistrement, et les implémentations de ces dépôts devront conserver immédiatement un objet dans la base de données. Toutefois, dans de nombreuses applications, nous ne souhaitons pas conserver les objets individuellement. Au lieu de cela, nous voulons donner vie aux objets, peut-être à partir de différents référentiels, modifier ces objets dans le cadre d’une activité d’entreprise, puis conserver tous les objets dans le cadre d’une opération atomique unique. Heureusement, il existe un modèle pour autoriser ce type de comportement.

### <a name="the-unit-of-work-pattern"></a>Modèle d’unité de travail

Fowler dit qu’une unité de travail conservera une liste d’objets affectés par une transaction commerciale et coordonne l’écriture des modifications et la résolution des problèmes d’accès concurrentiel. Il incombe à l’unité de travail de suivre les modifications apportées aux objets que nous avons apportées à la vie à partir d’un référentiel et de conserver les modifications que nous avons apportées aux objets quand nous indiquons à l’unité de travail de valider les modifications. Il incombe également à l’unité de travail de prendre les nouveaux objets que nous avons ajoutés à tous les référentiels, d’insérer les objets dans une base de données et de gérer également la suppression.

Si vous avez déjà effectué des tâches avec des jeux de données ADO.NET, vous êtes déjà familiarisé avec le modèle d’unité de travail. Les jeux de données ADO.NET pouvaient effectuer le suivi de nos mises à jour, suppressions et insertions d’objets DataRow et pouvaient (à l’aide d’un TableAdapter) réconcilier toutes les modifications apportées à une base de données. Toutefois, les objets DataSet modélisent un sous-ensemble déconnecté de la base de données sous-jacente. Le modèle unité de travail présente le même comportement, mais fonctionne avec les objets métier et les objets de domaine qui sont isolés du code d’accès aux données et ne connaissent pas la base de données.

Une abstraction pour modéliser l’unité de travail dans du code .NET peut se présenter comme suit :

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

En exposant les références de référentiel à partir de l’unité de travail, nous pouvons garantir qu’un seul objet d’unité de travail a la possibilité de suivre toutes les entités matérialisées lors d’une transaction commerciale. L’implémentation de la méthode Commit pour une unité de travail réelle est l’endroit où tout le Magic se produit pour rapprocher les modifications en mémoire avec la base de données. 

À partir d’une référence IUnitOfWork, le code peut apporter des modifications aux objets métier récupérés à partir d’un ou de plusieurs référentiels et enregistrer toutes les modifications à l’aide de l’opération de validation atomique.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Le modèle de chargement différé

Fowler utilise la charge différée de nom pour décrire « un objet qui ne contient pas toutes les données dont vous avez besoin, mais qui sait comment l’accéder ». Le chargement différé transparent est une fonctionnalité importante pour l’écriture de code d’entreprise testable et l’utilisation d’une base de données relationnelle. À titre d’exemple, considérez le code suivant.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Comment la collection des entrées de procédure est-elle remplie ? Il existe deux réponses possibles. L’une des réponses est que le dépôt de l’employé, lorsqu’il est invité à extraire un employé, émet une requête pour récupérer à la fois l’employé et les informations de carte de l’employé associées. Dans les bases de données relationnelles, cela nécessite généralement une requête avec une clause JOIN et peut entraîner la récupération d’informations supplémentaires par rapport aux besoins d’une application. Que se passe-t-il si l’application n’a jamais besoin de toucher la propriété de la fonction de la

Une deuxième réponse consiste à charger la propriété de la fonction de la requête « à la demande ». Ce chargement différé est implicite et transparent pour la logique métier, car le code n’appelle pas d’API spéciales pour récupérer les informations de carte de temps. Le code suppose que les informations de carte de temps sont présentes si nécessaire. Une magie est impliquée dans le chargement différé qui implique généralement l’interception de l’exécution d’appels de méthode. Le code d’interception est chargé de communiquer avec la base de données et de récupérer les informations de la carte de temps tout en laissant la logique métier libre pour être logique métier. Cette magie de chargement différé permet au code d’entreprise de s’isoler des opérations d’extraction de données et de se traduit par un code plus testable.

L’inconvénient d’une charge différée est que lorsqu’une application *a* besoin des informations de carte de temps, le code exécutera une requête supplémentaire. Ce n’est pas une préoccupation pour de nombreuses applications, mais pour les applications sensibles aux performances, en boucle sur un certain nombre d’objets Employee et en exécutant une requête pour récupérer des cartes de temps lors de chaque itération de la boucle (un problème souvent appelé problème de requête N + 1), le chargement différé est un glissement. Dans ces scénarios, une application peut souhaiter charger de manière dynamique les informations de carte de temps de la manière la plus efficace possible.

Heureusement, nous allons voir comment EF4 prend en charge à la fois les charges tardives implicites et les charges hâtif efficaces à mesure que nous passons à la section suivante et implémentons ces modèles.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implémentation de modèles avec l’Entity Framework

La bonne nouvelle, c’est que tous les modèles de conception que nous avons décrits dans la dernière section sont faciles à implémenter avec EF4. Pour démontrer que nous allons utiliser une simple application MVC ASP.NET pour modifier et afficher les employés et leurs informations de carte de temps associées. Nous allons commencer par utiliser les objets « Plain Old CLR Objects » (POCO) suivants. 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

Ces définitions de classe seront légèrement modifiées à mesure que nous explorerons les différentes approches et fonctionnalités de EF4, mais l’objectif est de conserver ces classes comme étant le plus possible (PI). Un objet PI ne sait pas *Comment*, ou même *si*, l’État qu’il contient réside dans une base de données. PI et POCO vont de pair avec des logiciels testables. Les objets utilisant une approche POCO sont moins limités, plus flexibles et plus faciles à tester, car ils peuvent fonctionner sans une base de données présente.

Une fois les POCO en place, nous pouvons créer un Entity Data Model (EDM) dans Visual Studio (voir la figure 1). Nous n’utiliserons pas le modèle EDM pour générer du code pour nos entités. Au lieu de cela, nous souhaitons utiliser les entités qui nous intéressent à la main. Nous n’utiliserons le modèle EDM que pour générer le schéma de base de données et fournir les métadonnées dont EF4 a besoin pour mapper les objets dans la base de données.

![test_01 EF](~/ef6/media/eftest-01.jpg)

**Figure 1**

Remarque : Si vous souhaitez développer le modèle EDM en premier, il est possible de générer du code POCO propre à partir du modèle EDM. Vous pouvez le faire avec une extension Visual Studio 2010 fournie par l’équipe de programmabilité des données. Pour télécharger l’extension, lancez le gestionnaire d’extensions à partir du menu outils de Visual Studio et recherchez « POCO » dans la galerie en ligne de modèles (voir figure 2). Plusieurs modèles POCO sont disponibles pour EF. Pour plus d’informations sur l’utilisation du modèle, consultez la rubrique « [procédure pas à pas : modèle POCO pour le Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)».

![test_02 EF](~/ef6/media/eftest-02.png)

**Figure 2**

À partir de ce point de départ POCO, nous explorerons deux approches différentes du code testable. La première approche que j’appelle l’approche EF, c’est qu’elle tire parti des abstractions de l’API Entity Framework pour implémenter des unités de travail et des dépôts. Dans la deuxième approche, nous allons créer nos propres abstractions de référentiel personnalisées, puis voir les avantages et les inconvénients de chaque approche. Nous allons commencer par explorer l’approche EF.  

### <a name="an-ef-centric-implementation"></a>Une implémentation d’EF centrée

Examinez l’action de contrôleur suivante à partir d’un projet MVC ASP.NET. L’action extrait un objet Employee et retourne un résultat pour afficher une vue détaillée de l’employé.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Le code est-il testable ? Il faut au moins deux tests pour vérifier le comportement de l’action. Tout d’abord, nous souhaitons vérifier que l’action retourne la bonne vue, un test facile. Nous aimerions également écrire un test pour vérifier que l’action récupère le bon employé et nous aimerions le faire sans exécuter de code pour interroger la base de données. N’oubliez pas que nous souhaitons isoler le code testé. L’isolation garantit que le test n’échoue pas en raison d’un bogue dans le code d’accès aux données ou la configuration de la base de données. Si le test échoue, nous savons que nous avons un bogue dans la logique du contrôleur, et non dans un composant système de niveau inférieur.

Pour atteindre l’isolation, nous avons besoin de certaines abstractions comme les interfaces présentées précédemment pour les dépôts et les unités de travail. N’oubliez pas que le modèle de référentiel est conçu pour faire l’objet d’un médiateur entre les objets de domaine et la couche de mappage de données. Dans ce scénario, EF4 *est* la couche de mappage des données et fournit déjà une abstraction de type dépôt nommée IObjectSet &lt; T &gt; (à partir de l’espace de noms System. Data. Objects). La définition de l’interface se présente comme suit.

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet &lt; T &gt; répond à la configuration requise pour un référentiel, car il ressemble à une collection d’objets (via IEnumerable &lt; T &gt; ) et fournit des méthodes pour ajouter et supprimer des objets de la collection simulée. Les méthodes d’attachement et de détachement exposent des fonctionnalités supplémentaires de l’API EF4. Pour utiliser IObjectSet &lt; T &gt; comme interface pour les dépôts, nous avons besoin d’une abstraction d’unité de travail pour lier les référentiels ensemble.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Une implémentation concrète de cette interface va communiquer avec SQL Server et est facile à créer à l’aide de la classe ObjectContext de EF4. La classe ObjectContext est l’unité de travail réelle dans l’API EF4.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

L’intégration d’un IObjectSet &lt; T &gt; à Life est aussi simple que l’appel de la méthode méthode CreateObjectSet de l’objet ObjectContext. En coulisses, l’infrastructure utilisera les métadonnées que nous avons fournies dans le modèle EDM pour produire un ObjectSet &lt; T concret &gt; . Nous allons utiliser le retour de l' &lt; interface IObjectSet T &gt; , car cela permet de préserver la testabilité dans le code client.

Cette implémentation concrète est utile en production, mais nous devons nous concentrer sur la façon dont nous allons utiliser notre abstraction IUnitOfWork pour faciliter les tests.

### <a name="the-test-doubles"></a>Les doubles de test

Pour isoler l’action du contrôleur, nous avons besoin de la possibilité de basculer entre l’unité de travail réelle (avec un ObjectContext) et une unité de travail de test double ou « factice » (en effectuant des opérations en mémoire). L’approche courante pour effectuer ce type de commutation consiste à ne pas laisser le contrôleur MVC instancier une unité de travail, mais à passer à la place l’unité de travail dans le contrôleur en tant que paramètre de constructeur.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Le code ci-dessus est un exemple d’injection de dépendances. Nous n’autorisons pas le contrôleur à créer sa dépendance (l’unité de travail), mais injectent la dépendance dans le contrôleur. Dans un projet MVC, il est courant d’utiliser une fabrique de contrôleurs personnalisée en association avec un conteneur d’inversion de contrôle (IoC) pour automatiser l’injection de dépendances. Ces rubriques n’entrent pas dans le cadre de cet article, mais vous pouvez en savoir plus en suivant les références à la fin de cet article.

Une implémentation fictive d’une unité de travail que nous pouvons utiliser pour le test peut ressembler à ce qui suit.

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

Notez que l’unité de travail factice expose une propriété validée. Il est parfois utile d’ajouter des fonctionnalités à une classe factice qui facilite les tests. Dans ce cas, il est facile d’observer si le code valide une unité de travail en vérifiant la propriété validée.

Nous aurons également besoin d’un faux IObjectSet &lt; T &gt; pour contenir les objets Employee et de la table de pointage en mémoire. Nous pouvons fournir une implémentation unique à l’aide de génériques.

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

Ce double de test délègue la majeure partie de son travail à un objet HashSet T sous-jacent &lt; &gt; . Notez que IObjectSet &lt; T &gt; requiert une contrainte générique appliquant t en tant que classe (un type référence) et nous oblige également à implémenter l’option IQueryable &lt; t &gt; . Il est facile de faire apparaître une collection en mémoire sous la forme d’un IQueryable &lt; T &gt; à l’aide de l’opérateur standard LINQ AsQueryable.

### <a name="the-tests"></a>Les tests

Les tests unitaires traditionnels utilisent une classe de test unique pour contenir tous les tests pour toutes les actions dans un seul contrôleur MVC. Nous pouvons écrire ces tests, ou n’importe quel type de test unitaire, à l’aide des substituts en mémoire que nous avons générés. Toutefois, pour cet article, nous allons éviter l’approche de la classe de test monolithique et regrouper nos tests pour vous concentrer sur une fonctionnalité spécifique.Par exemple, « Create New Employee » peut être la fonctionnalité que nous voulons tester, donc nous utiliserons une seule classe de test pour vérifier l’action de contrôleur unique responsable de la création d’un nouvel employé.

Il existe un code d’installation commun dont nous avons besoin pour toutes ces classes de test affinées. Par exemple, nous devons toujours créer nos référentiels en mémoire et l’unité de travail factice. Nous avons également besoin d’une instance du contrôleur Employee avec l’unité de travail factice injectée. Nous allons partager ce code d’installation commun entre les classes de test à l’aide d’une classe de base.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

L’objet « maman » que nous utilisons dans la classe de base est un modèle commun pour la création de données de test. Un objet maman contient des méthodes de fabrique pour instancier des entités de test à utiliser sur plusieurs contextes de test.

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

Nous pouvons utiliser EmployeeControllerTestBase comme classe de base pour un certain nombre de contextes de test (voir la figure 3). Chaque contexte de test teste une action de contrôleur spécifique. Par exemple, un seul contexte de test se concentrera sur le test de l’action de création utilisée lors d’une requête HTTP. (pour afficher la vue de la création d’un employé) et un autre contexte se concentrera sur l’action de création utilisée dans une requête HTTP Après (pour prendre les informations envoyées par l’utilisateur pour créer un employé). Chaque classe dérivée est uniquement responsable de l’installation nécessaire dans son contexte spécifique, et pour fournir les assertions nécessaires pour vérifier les résultats pour son contexte de test spécifique.

![test_03 EF](~/ef6/media/eftest-03.png)

**Figure 3**

La Convention d’affectation de noms et le style de test présentés ici ne sont pas requis pour le code testable, il s’agit d’une seule approche. La figure 4 montre les tests en cours d’exécution dans le plug-in Test Runner de jet cerveau pour Visual Studio 2010.

![test_04 EF](~/ef6/media/eftest-04.png)

**Figure 4**

Avec une classe de base pour gérer le code d’installation partagé, les tests unitaires pour chaque action du contrôleur sont petits et faciles à écrire. Les tests s’exécuteront rapidement (puisque nous effectuons des opérations en mémoire) et ne devraient pas échouer en raison d’une infrastructure ou de problèmes environnementaux non liés (car nous avons isolé l’unité testée).

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

Dans ces tests, la classe de base effectue la plupart des tâches d’installation. Souvenez-vous que le constructeur de classe de base crée le référentiel en mémoire, une unité de travail factice et une instance de la classe EmployeeController. La classe de test dérive de cette classe de base et se concentre sur les spécificités du test de la méthode Create. Dans ce cas, les spécificités s’appliquent aux étapes « arrange, Act et Assert » que vous verrez dans toutes les procédures de test unitaire :

-   Créez un objet nouvel employé pour simuler des données entrantes.
-   Appelez l’action Create du EmployeeController et transmettez nouvel employé.
-   Vérifiez que l’action créer produit les résultats attendus (l’employé apparaît dans le référentiel).

Ce que nous avons créé nous permet de tester n’importe quelle action EmployeeController. Par exemple, lorsque nous écrivons des tests pour l’action d’index du contrôleur Employee Controller, nous pouvons hériter de la classe de base test pour établir la même configuration de base pour nos tests. Là encore, la classe de base crée le référentiel en mémoire, l’unité de travail factice et une instance de EmployeeController. Les tests de l’action d’index doivent uniquement se concentrer sur l’appel de l’action d’index et le test des qualités du modèle retourné par l’action.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

Les tests que nous créons avec les substituts en mémoire sont orientés vers le test de l' *État* du logiciel. Par exemple, lors du test de l’action de création, nous souhaitons inspecter l’état du dépôt après l’exécution de l’action de création : le référentiel conserve-t-il le nouvel employé ?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Nous examinerons ultérieurement les tests basés sur l’interaction. Le test basé sur l’interaction vous demande si le code testé a appelé les méthodes appropriées sur nos objets et a passé les paramètres corrects. Pour le moment, nous allons passer à la couverture d’un autre modèle de conception : le chargement différé.

## <a name="eager-loading-and-lazy-loading"></a>Chargement hâtif et chargement différé

À un moment donné dans l’application Web MVC ASP.NET, nous pouvons souhaiter afficher les informations d’un employé et inclure les cartes de point associées de l’employé. Par exemple, il peut y avoir un affichage de résumé de la carte de temps qui indique le nom de l’employé et le nombre total de cartes de temps dans le système. Il existe plusieurs approches pour implémenter cette fonctionnalité.

### <a name="projection"></a>Projection

Une approche simple pour créer le résumé consiste à construire un modèle dédié aux informations que nous souhaitons afficher dans la vue. Dans ce scénario, le modèle peut ressembler à ce qui suit.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Notez que le EmployeeSummaryViewModel n’est pas une entité. en d’autres termes, il ne doit pas être conservé dans la base de données. Nous allons uniquement utiliser cette classe pour mélanger des données dans la vue d’une manière fortement typée. Le modèle de vue est semblable à un objet de transfert de données (DTO), car il ne contient aucun comportement (aucune méthode), uniquement des propriétés. Les propriétés contiendront les données que vous devez déplacer. Il est facile d’instancier ce modèle de vue à l’aide de l’opérateur de projection standard de LINQ (l’opérateur SELECT).

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

Il existe deux fonctionnalités notables pour le code ci-dessus. Tout d’abord, le code est facile à tester, car il est toujours facile à observer et à isoler. L’opérateur SELECT fonctionne tout aussi bien sur nos substituts en mémoire que sur l’unité de travail réelle.

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

La deuxième fonctionnalité notable est la manière dont le code permet à EF4 de générer une requête unique et efficace pour assembler les informations relatives aux employés et aux cartes de temps. Nous avons chargé les informations sur les employés et les informations de carte de temps dans le même objet sans utiliser d’API spéciales. Le code a simplement exprimé les informations dont il a besoin à l’aide d’opérateurs LINQ standard qui fonctionnent avec les sources de données en mémoire, ainsi que les sources de données distantes. EF4 a pu traduire les arborescences d’expressions générées par la requête LINQ et le \# compilateur C en une requête T-SQL unique et efficace.

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

Il y a d’autres fois que nous ne souhaitons pas travailler avec un modèle de vue ou un objet DTO, mais avec des entités réelles. Lorsque nous savons que nous avons besoin d’un employé *et* des cartes de l’employé, nous pouvons charger les données associées de manière discrète et efficace.

### <a name="explicit-eager-loading"></a>Chargement hâtif explicite

Lorsque nous souhaitons charger des informations d’entité connexes, nous avons besoin d’un mécanisme pour la logique métier (ou dans ce scénario, la logique d’action du contrôleur) pour exprimer sa volonté dans le référentiel. La classe EF4 ObjectQuery &lt; T &gt; définit une méthode Include pour spécifier les objets connexes à récupérer au cours d’une requête. N’oubliez pas que EF4 ObjectContext expose des entités via la classe concrète ObjectSet &lt; t &gt; qui hérite de ObjectQuery &lt; T &gt; .Si nous utilisions &lt; &gt; des références ObjectSet T dans notre action de contrôleur, nous pourrions écrire le code suivant pour spécifier un chargement hâtif d’informations de carte de temps pour chaque employé.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Toutefois, étant donné que nous essayons de garder notre code testable, nous n’exposez pas ObjectSet &lt; T &gt; depuis l’extérieur de la classe de l’unité de travail réelle. Au lieu de cela, nous nous appuyons sur l' &lt; interface IObjectSet t &gt; qui est plus facile à falsifier, mais IObjectSet &lt; t &gt; ne définit pas de méthode Include. La beauté de LINQ est que nous pouvons créer notre propre opérateur include.

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

Notez que cet opérateur include est défini en tant que méthode d’extension pour IQueryable &lt; t &gt; au lieu de IObjectSet &lt; t &gt; . Cela nous donne la possibilité d’utiliser la méthode avec un plus grand nombre de types possibles, notamment IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; et ObjectSet &lt; t &gt; . Dans le cas où la séquence sous-jacente n’est pas un EF4 ObjectQuery &lt; T authentique &gt; , il n’y a pas de dommages causés et l’opérateur include est une absence d’opération. Si la séquence sous-jacente *est* un ObjectQuery &lt; t &gt; (ou dérivée de OBJECTQUERY &lt; t &gt; ), EF4 verra notre exigence de données supplémentaires et formulera la requête SQL appropriée.

Avec ce nouvel opérateur en place, nous pouvons demander explicitement un chargement hâtif d’informations de carte de temps dans le référentiel.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Lorsqu’il est exécuté sur un ObjectContext réel, le code génère la requête unique suivante. La requête rassemble suffisamment d’informations de la base de données en un seul voyage pour matérialiser les objets des employés et remplir entièrement leur propriété de la table de temps.

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

La bonne nouvelle, c’est que le code à l’intérieur de la méthode d’action reste entièrement testable. Nous n’avons pas besoin de fournir des fonctionnalités supplémentaires pour notre substituts pour prendre en charge l’opérateur include. La mauvaise nouvelle, c’est que nous devions utiliser l’opérateur include à l’intérieur du code, nous souhaitions conserver l’ignore de la persistance. Il s’agit d’un excellent exemple du type de compromis que vous devrez évaluer lors de la création de code testable. Dans certains cas, vous devez laisser des problèmes de persistance en dehors de l’abstraction du référentiel pour atteindre les objectifs de performances.

L’alternative au chargement hâtif est le chargement différé. Le chargement différé signifie que nous n’avons *pas* besoin de notre code d’entreprise pour annoncer explicitement la nécessité de données associées. Au lieu de cela, nous utilisons nos entités dans l’application et si des données supplémentaires sont nécessaires Entity Framework chargera les données à la demande.

### <a name="lazy-loading"></a>Chargement différé

Il est facile d’imaginer un scénario dans lequel nous ne savons pas quelles données une partie de la logique métier aura besoin. Nous savons peut-être que la logique a besoin d’un objet Employee, mais nous pouvons créer des branches dans différents chemins d’exécution où certains de ces chemins nécessitent des informations de carte de l’employé et d’autres non. Les scénarios de ce type sont parfaits pour le chargement différé implicite, car les données s’affichent par magie en fonction des besoins.

Le chargement différé, également connu sous le nom de chargement différé, place certaines exigences sur nos objets d’entité. Les POCO avec l’ignorance de la persistance réelle ne représenteront aucune exigence de la couche de persistance, mais l’ignorance de la persistance est pratiquement impossible à atteindre.Au lieu de cela, nous mesurons l’ignorance de la persistance en degrés relatifs. Cela serait malheureux si nous avions besoin d’hériter d’une classe de base orientée persistance ou d’utiliser une collection spécialisée pour atteindre un chargement différé dans les POCO. Heureusement, EF4 a une solution moins intrusive.

### <a name="virtually-undetectable"></a>Pratiquement indétectables

Lors de l’utilisation d’objets POCO, EF4 peut générer dynamiquement des proxies d’exécution pour les entités. Ces proxies encapsulent de façon invisible les POCO matérialisés et fournissent des services supplémentaires en interceptant chaque propriété obtenir et définir l’opération pour effectuer un travail supplémentaire. L’un de ces services est la fonctionnalité de chargement différé que nous recherchons. Un autre service est un mécanisme de suivi des modifications efficace qui peut enregistrer lorsque le programme modifie les valeurs de propriété d’une entité. La liste des modifications est utilisée par ObjectContext pendant la méthode SaveChanges pour rendre persistantes toutes les entités modifiées à l’aide de commandes UPDATE.

Toutefois, pour que ces proxies fonctionnent, ils ont besoin d’un moyen de se raccorder à des opérations d’extraction et de définition de propriétés sur une entité, et les proxys atteignent cet objectif en remplaçant les membres virtuels. Par conséquent, si nous souhaitons avoir un chargement différé implicite et un suivi efficace des modifications, nous devons revenir à nos définitions de classe POCO et marquer les propriétés comme étant virtuelles.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Nous pouvons toujours indiquer que l’entité Employee est principalement ignorant la persistance. La seule exigence consiste à utiliser des membres virtuels et cela n’a aucun impact sur la testabilité du code. Nous n’avons pas besoin de dériver d’une classe de base spéciale, ni même d’utiliser une collection spéciale dédiée au chargement différé. Comme le montre le code, toute classe qui implémente ICollection &lt; T &gt; est disponible pour conserver les entités associées.

Il y a également une modification mineure que nous devons faire au sein de notre unité de travail. Le chargement différé est *désactivé* par défaut quand vous travaillez directement avec un objet ObjectContext. Il existe une propriété que nous pouvons définir sur la propriété ContextOptions pour activer le chargement différé, et nous pouvons définir cette propriété à l’intérieur de notre véritable unité de travail si vous souhaitez activer le chargement différé partout.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

Si le chargement différé implicite est activé, le code de l’application peut utiliser un employé et les cartes de temps associées de l’employé, tout en restant tranquillement ne connaissant pas le travail nécessaire à EF pour charger les données supplémentaires.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Le chargement différé rend le code de l’application plus facile à écrire et, avec la magie du proxy, le code reste entièrement testable. Les substituts en mémoire de l’unité de travail peuvent simplement précharger des entités factices avec des données associées lorsque cela est nécessaire pendant un test.

À ce stade, nous nous pencherons sur la création de référentiels à l’aide de IObjectSet &lt; T &gt; et nous examinerons des abstractions pour masquer tous les signes de l’infrastructure de persistance.

## <a name="custom-repositories"></a>Référentiels personnalisés

Lorsque nous avons présenté le modèle de conception d’unité de travail dans cet article, nous avons fourni un exemple de code pour ce à quoi peut ressembler l’unité de travail. Nous allons représenter cette idée originale à l’aide du scénario Employee et Employee Time Card que nous travaillons.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La principale différence entre cette unité de travail et l’unité de travail que nous avons créée dans la dernière section est la manière dont cette unité de travail n’utilise pas d’abstractions de l’infrastructure EF4 (il n’y a pas de IObjectSet &lt; T &gt; ). IObjectSet &lt; T &gt; fonctionne bien comme une interface de référentiel, mais l’API qu’il expose peut ne pas être parfaitement adaptée aux besoins de l’application. Dans cette approche à venir, nous allons représenter des dépôts à l’aide d’une &lt; abstraction IRepository T personnalisée &gt; .

De nombreux développeurs qui suivent la conception pilotée par test, la conception pilotée par le comportement et les méthodologies pilotées par domaine préfèrent l' &lt; approche IRepository T &gt; pour plusieurs raisons. Tout d’abord, &lt; l' &gt; interface IRepository T représente une couche « anti-corruption ». Comme décrit par Eric Evans dans son livre de conception piloté par domaine, une couche de lutte contre la corruption permet d’éloigner votre code de domaine des API d’infrastructure, comme une API de persistance. Deuxièmement, les développeurs peuvent créer des méthodes dans le référentiel qui répondent aux besoins exacts d’une application (telle qu’elle a été détectée pendant l’écriture de tests). Par exemple, il se peut que nous ayons souvent besoin de localiser une seule entité à l’aide d’une valeur d’ID. nous pouvons donc ajouter une méthode FindById à l’interface du référentiel.Notre &lt; définition IRepository T &gt; se présente comme suit.

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Notez que nous allons revenir à l’utilisation d’une &lt; interface T IQueryable &gt; pour exposer des collections d’entités. IQueryable &lt; T &gt; permet aux arborescences d’expression LINQ de circuler dans le fournisseur EF4 et de fournir au fournisseur une vue holistique de la requête. Une seconde option consisterait à retourner IEnumerable &lt; T &gt; , ce qui signifie que le fournisseur LINQ EF4 verra uniquement les expressions générées dans le référentiel. Les regroupements, ordonnancements et projection effectués en dehors du référentiel ne sont pas composés de la commande SQL envoyée à la base de données, ce qui peut nuire aux performances. En revanche, un référentiel renvoyant uniquement &lt; &gt; des résultats IEnumerable T ne vous étonnera jamais avec une nouvelle commande SQL. Les deux approches fonctionnent et les deux approches restent testables.

Il est facile de fournir une implémentation unique de l' &lt; interface IRepository T &gt; à l’aide de génériques et de l’API ObjectContext EF4.

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

L' &lt; approche IRepository T &gt; nous donne un contrôle supplémentaire sur nos requêtes, car un client doit appeler une méthode pour accéder à une entité. À l’intérieur de la méthode, nous pourrions fournir des contrôles supplémentaires et des opérateurs LINQ pour appliquer des contraintes d’application. Notez que l’interface a deux contraintes sur le paramètre de type générique. La première contrainte est la classe cons Tainted requise par ObjectSet &lt; T &gt; , et la deuxième contrainte force nos entités à implémenter IEntity – une abstraction créée pour l’application. L’interface IEntity force les entités à avoir une propriété ID lisible, et nous pouvons ensuite utiliser cette propriété dans la méthode FindById. IEntity est défini avec le code suivant.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity peut être considéré comme une faible violation de l’ignorance de la persistance, car nos entités sont requises pour implémenter cette interface. Rappelez-vous que l’ignorance de la persistance concerne les compromis, et que de nombreuses fonctionnalités FindById compenseront la contrainte imposée par l’interface. L’interface n’a aucun impact sur la testabilité.

L’instanciation d’un IRepository en temps réel &lt; &gt; nécessite un EF4 ObjectContext, de sorte qu’une implémentation de l’unité de travail concrète doit gérer l’instanciation.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>Utilisation du référentiel personnalisé

L’utilisation de notre référentiel personnalisé n’est pas très différente de celle d’un référentiel basé sur IObjectSet &lt; T &gt; . Au lieu d’appliquer des opérateurs LINQ directement à une propriété, nous devons d’abord appeler l’une des méthodes du référentiel pour récupérer une référence de &lt; T IQueryable &gt; .

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Notez que l’opérateur include personnalisé que nous avons implémenté précédemment fonctionnera sans modification. La méthode FindById du référentiel supprime la logique dupliquée des actions tentant de récupérer une entité unique.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Il n’existe aucune différence significative dans la testabilité des deux approches que nous avons examinées. Nous pourrions fournir des implémentations factices de IRepository &lt; T &gt; en générant des classes concrètes avec HashSet &lt; Employee &gt; , tout comme nous l’avons fait dans la dernière section. Toutefois, certains développeurs préfèrent utiliser des objets factices et des infrastructures d’objets factices au lieu de générer des substituts. Nous allons examiner l’utilisation de simulacres pour tester notre implémentation et discuter des différences entre les simulacres et les simulations dans la section suivante.

### <a name="testing-with-mocks"></a>Test avec des simulacres

Il existe différentes approches pour créer ce que Martin Fowler appelle un « test double ». Un double de test (comme un film stunt double) est un objet que vous créez pour « mettre en attente » pour les objets de production réels pendant les tests. Les dépôts en mémoire que nous avons créés sont des doubles de test pour les dépôts qui communiquent avec SQL Server. Nous avons vu comment utiliser ces doubles de test lors des tests unitaires pour isoler le code et faire en sorte que les tests s’exécutent rapidement.

Les doubles de test que nous avons créés ont des implémentations de travail réelles. En arrière-plan, chacun d’entre eux stocke une collection concrète d’objets, et ceux-ci ajoutent et suppriment des objets de cette collection au fur et à mesure que nous manipulons le référentiel pendant un test. Certains développeurs aiment créer leurs doubles de test de cette façon, avec un code réel et des implémentations de travail.Ces doubles de test sont ce que nous appelons les *substituts*. Ils ont des implémentations opérationnelles, mais ils ne sont pas assez réels pour une utilisation en production. Le référentiel factice n’écrit pas réellement dans la base de données. Le serveur SMTP factice n’envoie pas en fait un message électronique sur le réseau.

### <a name="mocks-versus-fakes"></a>Simulacres et substituts

Il existe un autre type de test double connu sous le nom de *fictif*. Alors que les substituts ont des implémentations opérationnelles, les simulacres ne sont pas implémentés. Avec l’aide d’une infrastructure d’objets factices, nous construisons ces objets factices au moment de l’exécution et les utilisons en tant que double de test. Dans cette section, nous allons utiliser l’infrastructure de simulation open source MOQ. Voici un exemple simple d’utilisation de MOQ pour créer dynamiquement un double de test pour un dépôt d’employés.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Nous demandons à MOQ pour une &lt; implémentation IRepository Employee &gt; et il en crée un de manière dynamique. Nous pouvons accéder à l’objet qui implémente IRepository &lt; Employee &gt; en accédant à la propriété Object de l' &lt; objet factice T &gt; . Il s’agit de cet objet interne que nous pouvons transmettre à nos contrôleurs, et ils ne savent pas s’il s’agit d’un double de test ou d’un référentiel réel. Nous pouvons appeler des méthodes sur l’objet de la même façon que nous appellerons des méthodes sur un objet avec une implémentation réelle.

Vous devez vous demander ce que fera le dépôt fictif quand nous invoquons la méthode Add. Étant donné qu’il n’y a pas d’implémentation derrière l’objet factice, Add n’a aucun effet. Il n’y a pas de collection concrète en arrière-plan, comme nous l’avons fait avec les substituts que nous avons écrits, l’employé est donc ignoré. Qu’en est-il de la valeur de retour de FindById ? Dans ce cas, l’objet factice fait la seule chose qu’il peut faire, qui retourne une valeur par défaut. Étant donné que nous retournons un type référence (un employé), la valeur de retour est une valeur null.

Les simulacres peuvent ne pas avoir de bruit ; Toutefois, nous n’avons pas parlé de deux autres fonctionnalités fictives. Tout d’abord, l’infrastructure MOQ enregistre tous les appels effectués sur l’objet factice. Plus tard dans le code, nous pouvons demander à MOQ si quelqu’un a appelé la méthode Add ou si quelqu’un a appelé la méthode FindById. Nous verrons plus tard comment nous pouvons utiliser cette fonctionnalité d’enregistrement « boîte noire » dans les tests.

La deuxième fonctionnalité intéressante est la façon dont nous pouvons utiliser MOQ pour programmer un objet fictif avec des *attentes*. Une attente indique à l’objet factice comment répondre à une interaction donnée. Par exemple, nous pouvons programmer une attente dans notre simulacre et lui demander de retourner un objet employé lorsqu’un utilisateur appelle FindById. L’infrastructure MOQ utilise une API d’installation et des expressions lambda pour programmer ces attentes.

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

Dans cet exemple, nous demandons à MOQ de créer dynamiquement un référentiel, puis de programmer le dépôt dans une attente. L’attente indique à l’objet factice de retourner un nouvel objet Employee avec une valeur d’ID de 5 lorsqu’un utilisateur appelle la méthode FindById en passant une valeur de 5. Ce test réussit, et nous n’avons pas besoin de créer une implémentation complète de factice IRepository &lt; t &gt; .

Nous allons revoir les tests que nous avons écrits précédemment et les réutiliser pour utiliser des simulacres au lieu de simulations. Comme précédemment, nous utilisons une classe de base pour configurer les éléments d’infrastructure courants dont nous avons besoin pour tous les tests du contrôleur.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

Le code d’installation reste quasiment le même. Au lieu d’utiliser des substituts, nous allons utiliser MOQ pour construire des objets factices. La classe de base fait en sorte que l’unité factice de travail retourne un référentiel fictif lorsque le code appelle la propriété Employees. Le reste de l’installation factice aura lieu à l’intérieur des contextes de test dédiés à chaque scénario spécifique. Par exemple, le contexte de test de l’action d’index configurera le référentiel fictif pour retourner une liste d’employés quand l’action appelle la méthode FindAll du référentiel fictif.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

À l’exception des attentes, nos tests sont similaires aux tests que nous avions auparavant. Toutefois, avec la capacité d’enregistrement d’un Framework fictif, nous pouvons aborder les tests à partir d’un angle différent. Nous allons examiner cette nouvelle perspective dans la section suivante.

### <a name="state-versus-interaction-testing"></a>État et tests d’interaction

Vous pouvez utiliser différentes techniques pour tester des logiciels avec des objets fictifs. Une approche consiste à utiliser des tests basés sur l’État, ce que nous avons fait dans ce document jusqu’à présent. Le test basé sur les États fait des assertions sur l’état du logiciel. Dans le dernier test, nous avons appelé une méthode d’action sur le contrôleur et effectué une assertion sur le modèle à générer. Voici d’autres exemples d’état de test :

-   Vérifiez que le référentiel contient le nouvel objet d’employé après l’exécution de Create.
-   Vérifiez que le modèle contient une liste de tous les employés après l’exécution de l’index.
-   Vérifiez que le dépôt ne contient pas d’employé donné après l’exécution de la suppression.

Une autre approche que vous verrez avec les objets factices consiste à vérifier les *interactions*. Bien que le test basé sur l’État fasse des assertions sur l’état des objets, le test basé sur l’interaction fait des assertions sur la manière dont les objets interagissent. Par exemple :

-   Vérifiez que le contrôleur appelle la méthode Add du référentiel lorsque Create s’exécute.
-   Vérifiez que le contrôleur appelle la méthode FindAll du référentiel lorsque l’index s’exécute.
-   Vérifiez que le contrôleur appelle la méthode Commit de l’unité de travail pour enregistrer les modifications lorsque la modification est exécutée.

Le test d’interaction requiert souvent moins de données de test, car il n’est pas possible de les percer à l’intérieur des collections et de vérifier les nombres. Par exemple, si nous savons que l’action Details appelle la méthode FindById d’un référentiel avec la valeur correcte, l’action se comporte probablement correctement. Nous pouvons vérifier ce comportement sans configurer les données de test à retourner à partir de FindById.

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

La seule configuration requise dans le contexte de test ci-dessus est celle fournie par la classe de base. Lorsque nous invoquons l’action du contrôleur, MOQ enregistre les interactions avec le référentiel fictif. À l’aide de l’API Verify de MOQ, nous pouvons demander à MOQ si le contrôleur a appelé FindById avec la valeur d’ID appropriée. Si le contrôleur n’a pas appelé la méthode ou a appelé la méthode avec une valeur de paramètre inattendue, la méthode Verify lèvera une exception et le test échouera.

Voici un autre exemple pour vérifier que l’action Create appelle commit sur l’unité de travail actuelle.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

L’un des risques avec les tests d’interaction est la tendance à définir des interactions. La capacité de l’objet factice à enregistrer et à vérifier chaque interaction avec l’objet factice ne signifie pas que le test doit essayer de vérifier chaque interaction. Certaines interactions sont des détails d’implémentation et vous devez uniquement vérifier les interactions *requises* pour répondre au test actuel.

Le choix entre les simulacres ou les substituts dépend en grande partie du système que vous testez et de vos préférences personnelles (ou de votre équipe). Les objets factices peuvent réduire considérablement la quantité de code dont vous avez besoin pour implémenter les doubles de test, mais il n’est pas tout à fait facile de programmer les attentes en matière de programmation et de vérification des interactions.

## <a name="conclusions"></a>Conclusions

Dans ce document, nous avons présenté plusieurs approches pour créer du code testable tout en utilisant le Entity Framework ADO.NET pour la persistance des données. Nous pouvons tirer parti des abstractions intégrées telles que IObjectSet &lt; t &gt; , ou créer vos propres abstractions comme IRepository &lt; t &gt; .Dans les deux cas, la prise en charge de POCO dans le ADO.NET Entity Framework 4,0 permet aux consommateurs de ces abstractions de rester persistants et d’être facilement testables. Des fonctionnalités EF4 supplémentaires telles que le chargement différé implicite permettent au code de service d’application et d’entreprise de fonctionner sans se soucier des détails d’une banque de données relationnelle. Enfin, les abstractions que nous créons sont faciles à imiter ou factices dans les tests unitaires, et nous pouvons utiliser ces doubles de test pour exécuter des tests rapides, très isolés et fiables.

### <a name="additional-resources"></a>Ressources supplémentaires

-   Robert C. Martin, « [le principe de responsabilité unique](https://www.objectmentor.com/resources/articles/srp.pdf)»
-   Martin Fowler, [catalogue des modèles](https://www.martinfowler.com/eaaCatalog/index.html) de l' *architecture des applications d’entreprise*
-   Griffin Caprio, " [injection de dépendances](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog sur la programmabilité des données, « [procédure pas à pas : développement piloté par les tests avec le Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)».
-   Blog sur la programmabilité des données, « [utilisation du référentiel et des modèles d’unité de travail avec Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)»
-   Aaron Jensen, « [Présentation des spécifications d’ordinateur](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)»
-   Eric Lee, « [BDD avec MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)»
-   Eric Evans, « [conception pilotée par domaine](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)»
-   Martin Fowler, « les [simulacres ne sont pas des stubs](https://martinfowler.com/articles/mocksArentStubs.html)»
-   Martin Fowler, « [test double](https://martinfowler.com/bliki/TestDouble.html)»
-   [MOQ](https://code.google.com/p/moq/)

### <a name="biography"></a>Biographie

Scott Allen est membre du personnel technique de Pluralsight et du fondateur de OdeToCode.com. Dans 15 ans de développement logiciel commercial, Scott a travaillé sur des solutions pour tous les éléments, des appareils embarqués 8 bits aux applications Web ASP.NET hautement évolutives. Vous pouvez contacter Scott sur son blog sur OdeToCode ou sur Twitter à l’adresse [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .
