---
title: Testabilité et cadre d’entité 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434311"
---
# <a name="testability-and-entity-framework-40"></a>Testabilité et cadre d’entité 4.0
Scott Allen

Date de publication : mai 2010

## <a name="introduction"></a>Introduction

Ce livre blanc décrit et démontre comment écrire du code testable avec le cadre d’entité ADO.NET 4.0 et Visual Studio 2010. Ce document n’essaie pas de se concentrer sur une méthodologie de test spécifique, comme la conception axée sur les tests (TDD) ou la conception axée sur le comportement (BDD). Au lieu de cela, ce document se concentrera sur la façon d’écrire du code qui utilise le cadre de l’entité ADO.NET mais reste facile à isoler et à tester de façon automatisée. Nous examinerons les modèles de conception communs qui facilitent les tests dans les scénarios d’accès aux données et nous verrons comment appliquer ces modèles lors de l’utilisation du cadre. Nous examinerons également les caractéristiques spécifiques du cadre pour voir comment ces fonctionnalités peuvent fonctionner dans le code testable.

## <a name="what-is-testable-code"></a>Qu’est-ce que le Code testable?

La possibilité de vérifier un logiciel à l’aide de tests unitaires automatisés offre de nombreux avantages souhaitables. Tout le monde sait que de bons tests permettra de réduire le nombre de défauts logiciels dans une application et d’augmenter la qualité de l’application - mais avoir des tests unitaires en place va bien au-delà de la simple recherche de bogues.

Une bonne suite de test unitaire permet à une équipe de développement de gagner du temps et de garder le contrôle du logiciel qu’elle crée. Une équipe peut apporter des modifications au code existant, refactor, refonte et logiciel de restructuration pour répondre à de nouvelles exigences, et ajouter de nouveaux composants dans une application tout en sachant que la suite de test peut vérifier le comportement de l’application. Les tests unitaires font partie d’un cycle de rétroaction rapide pour faciliter le changement et préserver la maintenance du logiciel à mesure que la complexité augmente.

Les tests unitaires ont toutefois un prix. Une équipe doit investir le temps de créer et de maintenir des tests unitaires. La quantité d’efforts nécessaires pour créer ces tests est directement liée à la **testabilité** du logiciel sous-jacent. Dans quelle mesure le logiciel est-il facile à tester ? Une équipe de conception de logiciels avec la testabilité à l’esprit créera des tests efficaces plus rapidement que l’équipe travaillant avec un logiciel non testable.

Microsoft a conçu le cadre d’entité ADO.NET 4.0 (EF4) avec la testabilité à l’esprit. Cela ne signifie pas que les développeurs écrira des tests unitaires contre le code-cadre lui-même. Au lieu de cela, les objectifs de testabilité pour EF4 facilitent la création d’un code testable qui s’appuie sur le cadre. Avant d’examiner des exemples spécifiques, il vaut la peine de comprendre les qualités du code testable.

### <a name="the-qualities-of-testable-code"></a>Les qualités du code testable

Le code facile à tester présente toujours au moins deux traits. Tout d’abord, le code testable est facile à **observer**. Compte tenu de certains ensembles d’entrées, il devrait être facile d’observer la sortie du code. Par exemple, le test de la méthode suivante est facile car la méthode renvoie directement le résultat d’un calcul.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Tester une méthode est difficile si la méthode écrit la valeur calculée dans une prise réseau, une table de base de données ou un fichier comme le code suivant. Le test doit effectuer des travaux supplémentaires pour récupérer la valeur.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

Deuxièmement, le code testable est facile à **isoler**. Utilisons le pseudo-code suivant comme un mauvais exemple de code testable.

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

La méthode est facile à observer - nous pouvons passer dans une police d’assurance et de vérifier la valeur de retour correspond à un résultat attendu. Cependant, pour tester la méthode, nous aurons besoin d’avoir une base de données installée avec le schéma correct, et configurer le serveur SMTP au cas où la méthode tente d’envoyer un e-mail.

Le test unitaire ne veut vérifier que la logique de calcul à l’intérieur de la méthode, mais le test peut échouer parce que le serveur de messagerie est hors ligne, ou parce que le serveur de base de données déplacé. Ces deux échecs ne sont pas liés au comportement que le test veut vérifier. Le comportement est difficile à isoler.

Les développeurs de logiciels qui s’efforcent d’écrire du code testable s’efforcent souvent de maintenir une séparation des préoccupations dans le code qu’ils écrivent. La méthode ci-dessus devrait se concentrer sur les calculs de l’entreprise et déléguer les détails de la base de données et de la mise en œuvre par courriel à d’autres composantes. Robert C. Martin appelle cela le principe de la responsabilité unique. Un objet doit encapsuler une responsabilité unique et étroite, comme calculer la valeur d’une police. Tous les autres travaux de base de données et de notification doivent être la responsabilité d’un autre objet. Code écrit de cette façon est plus facile à isoler parce qu’il est axé sur une seule tâche.

En .NET, nous avons les abstractions dont nous avons besoin pour suivre le principe de responsabilité unique et parvenir à l’isolement. Nous pouvons utiliser les définitions d’interface et forcer le code à utiliser l’abstraction de l’interface au lieu d’un type concret. Plus tard dans ce document, nous verrons comment une méthode comme le mauvais exemple présenté ci-dessus peut fonctionner avec des interfaces qui *ressemblent* à ils vont parler à la base de données. Au moment du test, cependant, nous pouvons substituer une implémentation factice qui ne parle pas à la base de données, mais tient plutôt des données en mémoire. Cette implémentation factice isolera le code des problèmes non liés dans le code d’accès aux données ou la configuration de base de données.

L’isolement a d’autres avantages. Le calcul de l’entreprise dans la dernière méthode ne devrait prendre que quelques millisecondes pour exécuter, mais le test lui-même pourrait s’exécuter pendant plusieurs secondes que le code saute autour du réseau et parle à divers serveurs. Les tests unitaires doivent être effectués rapidement pour faciliter les petits changements. Les tests unitaires doivent également être reproductibles et ne pas échouer parce qu’un composant sans rapport avec le test a un problème. Écrire du code qui est facile à observer et à isoler signifie que les développeurs auront plus de facilité à écrire des tests pour le code, passer moins de temps à attendre que les tests s’exécutent, et surtout, passer moins de temps à traquer les bogues qui n’existent pas.

Espérons que vous pouvez apprécier les avantages de tester et de comprendre les qualités que le code testable montre. Nous sommes sur le point d’aborder la façon d’écrire du code qui fonctionne avec EF4 pour enregistrer des données dans une base de données tout en restant observable et facile à isoler, mais d’abord nous allons réduire notre objectif pour discuter des conceptions testables pour l’accès aux données.

## <a name="design-patterns-for-data-persistence"></a>Modèles de conception pour la persistance des données

Les deux mauvais exemples présentés précédemment avaient trop de responsabilités. Le premier mauvais exemple a dû effectuer un calcul *et* écrire à un fichier. Le deuxième mauvais exemple a dû lire les données d’une base de données *et* effectuer un calcul d’entreprise *et* envoyer des courriels. En concevant des méthodes plus petites qui séparent les préoccupations et déléguent la responsabilité à d’autres composants, vous ferez de grands progrès vers l’écriture de code testable. L’objectif est de construire la fonctionnalité en composant des actions à partir d’abstractions petites et ciblées.

Quand il s’agit de la persistance des données les petites abstractions ciblées que nous recherchons sont si communs qu’ils ont été documentés comme modèles de conception. Le livre de Martin Fowler Patterns of Enterprise Application Architecture a été la première œuvre à décrire ces modèles imprimés. Nous fournirons une brève description de ces modèles dans les sections suivantes avant de montrer comment ces ADO.NET Cadre d’entité met en œuvre et fonctionne avec ces modèles.

### <a name="the-repository-pattern"></a>The Repository Pattern

Selon M. Fowler, un référentiel « se médiate entre les couches de cartographie du domaine et de données à l’aide d’une interface de type collection pour accéder aux objets de domaine ». Le but du modèle de dépôt est d’isoler le code des détails de l’accès aux données, et comme nous l’avons vu l’isolement plus tôt est un trait requis pour la testabilité.

La clé de l’isolement est la façon dont le référentiel expose les objets à l’aide d’une interface de collection. La logique que vous écrivez pour utiliser le référentiel n’a aucune idée de la façon dont le référentiel matérialisera les objets que vous demandez. Le référentiel peut parler à une base de données, ou il pourrait simplement retourner des objets d’une collection en mémoire. Tout ce que votre code doit savoir, c’est que le référentiel semble maintenir la collection, et vous pouvez récupérer, ajouter et supprimer des objets de la collection.

Dans les applications .NET existantes, un référentiel en béton hérite souvent d’une interface générique comme suit :

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Nous apporterons quelques modifications à la définition de l’interface lorsque nous fournirons une implémentation pour EF4, mais le concept de base reste le même. Code peut utiliser un référentiel concret implémentant cette interface pour récupérer une entité par sa valeur clé principale, pour récupérer une collection d’entités basées sur l’évaluation d’un prédicat, ou tout simplement récupérer toutes les entités disponibles. Le code peut également ajouter et supprimer des entités via l’interface de dépôt.

Compte tenu d’un IRepository d’objets employés, le code peut effectuer les opérations suivantes.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Étant donné que le code utilise une interface (IRepository of Employee), nous pouvons fournir au code différentes implémentations de l’interface. Une mise en œuvre peut être une implémentation soutenue par EF4 et des objets persistants dans une base de données Microsoft SQL Server. Une mise en œuvre différente (que nous utilisons pendant les tests) peut être soutenue par une liste mémoire des objets des employés. L’interface aidera à atteindre l’isolement dans le code.

Remarquez que l’interface IRepository&lt;T&gt; n’expose pas une opération Save. Comment mettre à jour les objets existants ? Vous pourriez rencontrer des définitions IRepository qui incluent l’opération Save, et les implémentations de ces dépôts devront immédiatement persister un objet dans la base de données. Cependant, dans de nombreuses applications, nous ne voulons pas persister des objets individuellement. Au lieu de cela, nous voulons donner vie à des objets, peut-être à partir de différents dépôts, modifier ces objets dans le cadre d’une activité commerciale, puis persister tous les objets dans le cadre d’une seule opération atomique. Heureusement, il existe un modèle pour permettre ce type de comportement.

### <a name="the-unit-of-work-pattern"></a>L’unité de modèle de travail

M. Fowler affirme qu’une unité de travail « maintiendra une liste d’objets touchés par une transaction commerciale et coordonnera l’écriture à partir de modifications et la résolution des problèmes de concurrence ». Il incombe à l’unité de travail de suivre les changements apportés aux objets que nous apportons à la vie à partir d’un référentiel et de persister tous les changements que nous avons apportés aux objets lorsque nous disons à l’unité de travail d’engager les changements. Il est également de la responsabilité de l’unité de travail de prendre les nouveaux objets que nous avons ajoutés à tous les dépôts et d’insérer les objets dans une base de données, ainsi que la suppression des manges.

Si vous avez déjà travaillé avec ADO.NET DataSets, vous serez déjà familier avec l’unité de modèle de travail. ADO.NET DataSets avait la capacité de suivre nos mises à jour, suppressions et insertion d’objets DataRow et pouvait (avec l’aide d’un TableAdapter) concilier toutes nos modifications à une base de données. Toutefois, les objets DataSet modéliser un sous-ensemble déconnecté de la base de données sous-jacente. L’unité de modèle de travail présente le même comportement, mais fonctionne avec des objets d’affaires et des objets de domaine qui sont isolés du code d’accès aux données et ignorants de la base de données.

Une abstraction pour modéliser l’unité de travail en code .NET peut ressembler à ce qui suit:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

En exposant les références de dépôt de l’unité de travail, nous pouvons nous assurer qu’une seule unité d’objet de travail a la capacité de suivre toutes les entités matérialisées au cours d’une transaction commerciale. La mise en œuvre de la méthode Commit pour une véritable unité de travail est l’endroit où toute la magie se trouve à concilier les changements de mémoire avec la base de données. 

Compte tenu d’une référence IUnitOfWork, le code peut apporter des modifications aux objets d’affaires récupérés à partir d’un ou plusieurs dépôts et enregistrer toutes les modifications à l’aide de l’opération Atomic Commit.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Le modèle de charge paresseux

Fowler utilise le nom de charge paresseuse pour décrire "un objet qui ne contient pas toutes les données dont vous avez besoin, mais sait comment l’obtenir". Le chargement paresseux transparent est une caractéristique importante à avoir lors de la rédaction de code d’affaires testable et en travaillant avec une base de données relationnelle. À titre d’exemple, considérez le code suivant.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Comment la collection TimeCards est-elle peuplée? Il y a deux réponses possibles. Une réponse est que le dépôt de l’employé, lorsqu’on lui demande d’aller chercher un employé, émet une requête pour récupérer à la fois l’employé ainsi que les renseignements connexes de la carte de temps de l’employé. Dans les bases de données relationnelles, cela nécessite généralement une requête avec une clause JOIN et peut entraîner la récupération de plus d’informations qu’un besoin d’application. Que se passe-t-il si l’application n’a jamais besoin de toucher la propriété TimeCards ?

Une deuxième réponse est de charger la propriété TimeCards "sur demande". Ce chargement paresseux est implicite et transparent à la logique métier parce que le code n’invoque pas des API spéciales pour récupérer les informations de carte de temps. Le code suppose que les informations de carte de temps sont présentes en cas de besoin. Il ya une certaine magie impliquée dans le chargement paresseux qui implique généralement l’interception de la méthode d’invocations. Le code d’interception est responsable de parler à la base de données et de récupérer des informations de carte de temps tout en laissant la logique d’entreprise libre d’être logique d’affaires. Cette magie de charge paresseuse permet au code d’entreprise de s’isoler des opérations de récupération de données et se traduit par plus de code testable.

L’inconvénient d’une charge paresseuse est que lorsqu’une application *a* besoin des informations de carte de temps, le code exécutera une requête supplémentaire. Ce n’est pas une préoccupation pour de nombreuses applications, mais pour les applications sensibles aux performances ou des applications en boucle à travers un certain nombre d’objets des employés et l’exécution d’une requête pour récupérer les cartes de temps au cours de chaque itération de la boucle (un problème souvent appelé le problème de requête N '1), le chargement paresseux est une traînée. Dans ces scénarios, une application peut vouloir charger avec impatience les informations de carte de temps de la manière la plus efficace possible.

Heureusement, nous verrons comment EF4 prend en charge à la fois les charges paresseuses implicites et les charges enthousiastes efficaces que nous nous déplaçons dans la section suivante et mettre en œuvre ces modèles.

## <a name="implementing-patterns-with-the-entity-framework"></a>Mise en œuvre des modèles avec le Cadre d’entité

Les bonnes nouvelles sont que tous les modèles de conception que nous avons décrit dans la dernière section sont simples à mettre en œuvre avec EF4. Pour démontrer que nous allons utiliser une application MVC simple ASP.NET pour modifier et afficher les employés et les informations de leur carte de temps. Nous allons commencer par utiliser les « objets CLR anciens » suivants (POCOs). 

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

Ces définitions de classe changeront légèrement au fur et à mesure que nous explorerons différentes approches et caractéristiques d’EF4, mais l’intention est de garder ces classes aussi ignorantes de persistance (PI) que possible. Un objet PI ne sait pas *comment,* ou même *si,* l’état qu’il détient vit à l’intérieur d’une base de données. LES PI et poCOs vont de pair avec les logiciels testables. Les objets utilisant une approche POCO sont moins contraints, plus flexibles et plus faciles à tester parce qu’ils peuvent fonctionner sans une base de données présente.

Avec les POCO en place, nous pouvons créer un modèle de données d’entité (EDM) dans Visual Studio (voir figure 1). Nous n’utiliserons pas l’EDM pour générer du code pour nos entités. Au lieu de cela, nous voulons utiliser les entités que nous fabriquons avec amour à la main. Nous n’utiliserons l’EDM que pour générer notre schéma de base de données et fournir les métadonnées EF4 pour cartographier les objets dans la base de données.

![ef test_01](~/ef6/media/eftest-01.jpg)

**La figure 1**

Remarque : si vous souhaitez d’abord développer le modèle EDM, il est possible de générer du code POCO propre à partir de l’EDM. Vous pouvez le faire avec une extension Visual Studio 2010 fournie par l’équipe De programmabilité des données. Pour télécharger l’extension, lancez le gestionnaire d’extension à partir du menu Tools dans Visual Studio et recherchez la galerie en ligne de modèles pour "POCO" (Voir la figure 2). Il existe plusieurs modèles POCO disponibles pour EF. Pour plus d’informations sur l’utilisation du modèle, voir « [Procédure pas à pas : modèle DE l’OCO pour le cadre d’entité](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)».

![ef test_02](~/ef6/media/eftest-02.png)

**Figure 2**

À partir de ce point de départ du BCO, nous explorerons deux approches différentes du code testable. La première approche que j’appelle l’approche EF parce qu’elle tire parti des abstractions de l’API cadre d’entité pour mettre en œuvre des unités de travail et de dépôts. Dans la deuxième approche, nous allons créer nos propres abstractions de dépôt personnalisé et ensuite voir les avantages et les inconvénients de chaque approche. Nous allons commencer par explorer l’approche EF.  

### <a name="an-ef-centric-implementation"></a>Une mise en œuvre EF Centric

Considérez l’action suivante du contrôleur d’un projet de ASP.NET MVC. L’action récupère un objet de l’employé et renvoie un résultat pour afficher une vue détaillée de l’employé.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Le code est-il testable ? Il y a au moins deux tests dont nous aurions besoin pour vérifier le comportement de l’action. Tout d’abord, nous aimerions vérifier que l’action renvoie la vue correcte - un test facile. Nous aimerions également passer un test pour vérifier que l’action récupère l’employé correct, et nous aimerions le faire sans exécuter de code pour interroger la base de données. Rappelez-vous que nous voulons isoler le code à l’essai. L’isolement garantira que le test ne manque pas en raison d’un bogue dans le code d’accès aux données ou la configuration de base de données. Si le test échoue, nous saurons que nous avons un bogue dans la logique du contrôleur, et non dans un composant système de niveau inférieur.

Pour parvenir à l’isolement, nous aurons besoin de quelques abstractions comme les interfaces que nous avons présentées plus tôt pour les dépôts et les unités de travail. N’oubliez pas que le modèle de référentiel est conçu pour être médiateur entre les objets de domaine et la couche de cartographie des données. Dans ce scénario EF4 *est* la couche de cartographie des données, et&lt;fournit&gt; déjà une abstraction de type référentiel nommé IObjectSet T (à partir de l’espace de nom System.Data.Objects). La définition de l’interface ressemble à ce qui suit.

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

IObjectSet&lt;&gt; T répond aux exigences d’un référentiel car il ressemble à&lt;&gt;une collection d’objets (via IEnumerable T ) et fournit des méthodes pour ajouter et supprimer des objets de la collection simulée. Les méthodes Attach et Detach exposent les capacités supplémentaires de l’API EF4. Pour utiliser IObjectSet&lt;T&gt; comme interface pour les dépôts, nous avons besoin d’une unité d’abstraction de travail pour lier les dépôts ensemble.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Une implémentation concrète de cette interface parlera à SQL Server et est facile à créer en utilisant la classe ObjectContext à partir d’EF4. La classe ObjectContext est la véritable unité de travail de l’API EF4.

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

Donner vie&lt;&gt; à un T IObjectSet est aussi facile que d’invoquer la méthode CreateObjectSet de l’objet ObjectContext. Dans les coulisses, le cadre utilisera les métadonnées que nous&lt;&gt;avons fournies dans l’EDM pour produire un objet en bétonSet T . Nous nous en tiendrons au&lt;&gt; retour de l’interface IObjectSet T car elle aidera à préserver la testabilité du code client.

Cette mise en œuvre concrète est utile dans la production, mais nous devons nous concentrer sur la façon dont nous allons utiliser notre abstraction IUnitOfWork pour faciliter les tests.

### <a name="the-test-doubles"></a>Le test double

Pour isoler l’action du contrôleur, nous aurons besoin de la possibilité de basculer entre l’unité réelle de travail (soutenue par un objectContexte) et une unité de travail double ou « faux » de test (opérations de mise en mémoire). L’approche commune pour effectuer ce type de commutation est de ne pas laisser le contrôleur MVC instantané une unité de travail, mais plutôt passer l’unité de travail dans le contrôleur comme un paramètre constructeur.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Le code ci-dessus est un exemple d’injection de dépendance. Nous ne permettons pas au contrôleur de créer sa dépendance (l’unité de travail) mais d’injecter la dépendance dans le contrôleur. Dans un projet MVC, il est courant d’utiliser une usine de contrôleurs personnalisés en combinaison avec un conteneur d’inversion de contrôle (IoC) pour automatiser l’injection de dépendance. Ces sujets sont au-delà de la portée de cet article, mais vous pouvez en savoir plus en suivant les références à la fin de cet article.

Une fausse unité de mise en œuvre du travail que nous pouvons utiliser pour les tests pourrait ressembler à ce qui suit.

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

Notez que la fausse unité de travail expose une propriété commise. Il est parfois utile d’ajouter des fonctionnalités à une classe de faux qui facilitent les tests. Dans ce cas, il est facile d’observer si le code commet une unité de travail en vérifiant la propriété engagée.

Nous aurons également besoin d’un&lt;&gt; faux IObjectSet T pour contenir en mémoire les objets Employee et TimeCard. Nous pouvons fournir une mise en œuvre unique à l’aide de génériques.

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

Ce test double la majeure partie de son&lt;&gt; travail à un objet HashSet T sous-jacent. &lt;Notez que IObjectSet T&gt; nécessite une contrainte générique appliquant T comme une classe (un&lt;&gt;type de référence), et nous oblige également à mettre en œuvre IQueryable T . Il est facile de faire apparaître une collection en&lt;&gt; mémoire comme un T IQueryable en utilisant l’opérateur LINQ standard AsQueryable.

### <a name="the-tests"></a>Les tests

Les tests unitaires traditionnels utiliseront une seule classe de test pour tenir tous les tests pour toutes les actions dans un seul contrôleur MVC. Nous pouvons écrire ces tests, ou n’importe quel type de test unitaire, en utilisant les faux de mémoire que nous avons construits. Cependant, pour cet article, nous éviterons l’approche monolithique de classe de test et au lieu de regrouper nos tests pour se concentrer sur un morceau spécifique de fonctionnalité.Par exemple, « créer un nouvel employé » peut être la fonctionnalité que nous voulons tester, de sorte que nous allons utiliser une seule classe de test pour vérifier l’action de contrôleur unique responsable de la création d’un nouvel employé.

Il y a un code d’installation commun dont nous avons besoin pour toutes ces classes de test à grain fin. Par exemple, nous avons toujours besoin de créer nos dépôts en mémoire et de fausses unités de travail. Nous avons également besoin d’un cas du contrôleur des employés avec la fausse unité de travail injectée. Nous partagerons ce code de configuration commun entre les classes de test en utilisant une classe de base.

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

La « mère objet » que nous utilisons dans la classe de base est un modèle commun pour créer des données de test. Une mère objet contient des méthodes d’usine pour instantané des entités de test pour une utilisation à travers plusieurs appareils de test.

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

Nous pouvons utiliser la EmployeeControllerTestBase comme classe de base pour un certain nombre de matchs d’essai (voir la figure 3). Chaque luminaire d’essai testera une action spécifique du contrôleur. Par exemple, un appareil de test se concentrera sur le test de l’action Créer utilisé lors d’une demande HTTP GET (pour afficher la vue pour créer un employé), et un appareil différent se concentrera sur l’action Créer utilisé dans une demande HTTP POST (pour prendre les informations soumises par l’utilisateur pour créer un employé). Chaque classe dérivée n’est responsable que de la configuration nécessaire dans son contexte spécifique, et de fournir les affirmations nécessaires pour vérifier les résultats de son contexte de test spécifique.

![ef test_03](~/ef6/media/eftest-03.png)

**Figure 3**

La convention de nommage et le style de test présenté ici n’est pas nécessaire pour le code testable - c’est juste une approche. La figure 4 montre les tests en cours d’exécution dans le plugin jet Brains Resharper coureur d’essai pour Visual Studio 2010.

![ef test_04](~/ef6/media/eftest-04.png)

**Figure 4**

Avec une classe de base pour gérer le code de configuration partagé, les tests unitaires pour chaque action de contrôleur sont petits et faciles à écrire. Les tests s’exécuteront rapidement (puisque nous effectuons des opérations en mémoire), et ne devraient pas échouer en raison d’infrastructures ou de préoccupations environnementales indépendantes (parce que nous avons isolé l’unité à l’essai).

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

Dans ces tests, la classe de base fait la plupart du travail d’installation. Rappelez-vous que le constructeur de la classe de base crée le référentiel en mémoire, une fausse unité de travail et un exemple de la classe EmployeeController. La classe de test dérive de cette classe de base et se concentre sur les spécificités de l’essai de la méthode Créer. Dans ce cas, les détails se résument aux étapes « organiser, agir et affirmer » que vous verrez dans n’importe quelle procédure de test unitaire :

-   Créez un nouvel objet d’emploi pour simuler les données entrantes.
-   Invoquez l’action Créer du EmployeeController et passer dans le nouvel employé.
-   Vérifier l’action Créer produit les résultats attendus (l’employé apparaît dans le référentiel).

Ce que nous avons construit nous permet de tester l’une ou l’autre des actions employeeController. Par exemple, lorsque nous écrivons des tests pour l’action Index du contrôleur des employés, nous pouvons hériter de la classe de base de test pour établir la même configuration de base pour nos tests. Encore une fois, la classe de base créera le référentiel en mémoire, la fausse unité de travail, et un exemple de l’EmployeeController. Les tests d’action Index n’ont qu’à se concentrer sur l’invocation de l’action Index et de tester les qualités du modèle les retours d’action.

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

Les tests que nous créons avec des faux en mémoire sont orientés vers le test de *l’état* du logiciel. Par exemple, lorsque nous testons l’action Créer, nous voulons inspecter l’état du référentiel après l’exécution de l’action de création , le référentiel tient-il le nouvel employé ?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Plus tard, nous examinerons les tests basés sur l’interaction. Les tests basés sur l’interaction demanderont si le code testé a invoqué les méthodes appropriées sur nos objets et a passé les paramètres corrects. Pour l’instant, nous allons passer sur la couverture un autre modèle de conception - la charge paresseuse.

## <a name="eager-loading-and-lazy-loading"></a>Chargement et chargement paresseux impatients

À un moment donné dans la ASP.NET application Web MVC, nous pourrions souhaiter afficher les informations d’un employé et inclure les cartes de temps associées de l’employé. Par exemple, nous pouvons avoir un affichage sommaire de carte de temps qui indique le nom de l’employé et le nombre total de cartes de temps dans le système. Il existe plusieurs approches que nous pouvons adopter pour mettre en œuvre cette fonctionnalité.

### <a name="projection"></a>Projection

Une approche facile pour créer le résumé est de construire un modèle dédié à l’information que nous voulons afficher dans la vue. Dans ce scénario, le modèle pourrait ressembler à ce qui suit.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Notez que le EmployeeSummaryViewModel n’est pas une entité - en d’autres termes, ce n’est pas quelque chose que nous voulons persister dans la base de données. Nous n’utiliserons cette classe que pour mélanger les données dans la vue d’une manière fortement tapée. Le modèle de vue est comme un objet de transfert de données (DTO) parce qu’il ne contient aucun comportement (pas de méthodes) - seulement les propriétés. Les propriétés conserveront les données dont nous avons besoin pour nous déplacer. Il est facile d’instantanéiser ce modèle de vue en utilisant l’opérateur de projection standard de LINQ, l’opérateur Select.

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

Il ya deux caractéristiques notables au code ci-dessus. Tout d’abord , le code est facile à tester parce qu’il est encore facile à observer et à isoler. L’opérateur Select travaille tout aussi bien contre nos faux en mémoire qu’il le fait contre la véritable unité de travail.

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

La deuxième caractéristique notable est la façon dont le code permet à EF4 de générer une requête unique et efficace pour assembler les informations des employés et des cartes de temps ensemble. Nous avons chargé les informations des employés et les informations de carte de temps dans le même objet sans utiliser d’API spéciales. Le code s’est contenté d’exprimer l’information dont il a besoin en utilisant des opérateurs LINQ standard qui travaillent contre les sources de données en mémoire ainsi que les sources de données à distance. EF4 a été en mesure de traduire les arbres\# d’expression générés par la requête LINQ et le compilateur C en une requête T-SQL unique et efficace.

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

Il ya d’autres moments où nous ne voulons pas travailler avec un modèle de vue ou objet DTO, mais avec des entités réelles. Lorsque nous savons que nous avons besoin d’un employé *et* des cartes de temps de l’employé, nous pouvons charger avec empressement les données connexes d’une manière discrète et efficace.

### <a name="explicit-eager-loading"></a>Chargement enthousiaste explicite

Lorsque nous voulons charger avec empressement les informations d’entités connexes, nous avons besoin d’un mécanisme de logique d’entreprise (ou dans ce scénario, logique d’action de contrôleur) pour exprimer son désir au référentiel. La classe EF4&lt;&gt; ObjectQuery T définit une méthode Inclure pour spécifier les objets connexes à récupérer lors d’une requête. Rappelez-vous l’objet EF4Context expose les&lt;&gt; entités via la classe&lt;&gt;concrète ObjectSet T qui hérite de ObjectQuery T .Si nous utilisions les références ObjectSet&lt;T&gt; dans notre action de contrôleur, nous pourrions écrire le code suivant pour spécifier une charge avide d’informations de carte de temps pour chaque employé.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Cependant, puisque nous essayons de garder notre code testable, nous n’exposons pas ObjectSet&lt;T&gt; de l’extérieur de la véritable unité de classe de travail. Au lieu de cela, nous&lt;&gt; nous appuyons sur l’interface IObjectSet T qui est plus facile à simuler, mais IObjectSet&lt;T&gt; ne définit pas une méthode Inclure. La beauté de LINQ, c’est que nous pouvons créer notre propre opérateur Inclure.

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

Notez que cet opérateur Inclure est défini&lt;comme&gt; une méthode d’extension pour IQueryable T au lieu d’IObjectSet&lt;T&gt;. Cela nous donne la possibilité d’utiliser la méthode avec un&lt;plus&gt;large éventail&lt;de&gt;types possibles, y compris IQueryable T , IObjectSet T , ObjectQuery&lt;T&gt;, et ObjectSet&lt;T&gt;. Dans le cas où la séquence sous-jacente n’est pas un véritable EF4 ObjectQuery&lt;T&gt;, alors il n’y a pas de mal fait et l’opérateur Inclure est un no-op. Si la *is* séquence sous-jacente&lt;&gt; est un ObjectQuery&lt;&gt;T (ou dérivé de ObjectQuery T ), alors EF4 verra notre exigence de données supplémentaires et formulera la requête SQL appropriée.

Avec ce nouvel opérateur en place, nous pouvons explicitement demander une charge avide d’informations de carte de temps du référentiel.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Lorsqu’il est exécuté contre un vrai ObjectContext, le code produit la requête unique suivante. La requête recueille suffisamment d’informations à partir de la base de données en un seul voyage pour matérialiser les objets de l’employé et remplir entièrement leur propriété TimeCards.

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

La bonne nouvelle est que le code à l’intérieur de la méthode d’action reste entièrement testable. Nous n’avons pas besoin de fournir des fonctionnalités supplémentaires pour nos faux pour soutenir l’opérateur Inclure. La mauvaise nouvelle, c’est que nous avons dû utiliser l’opérateur Inclure à l’intérieur du code que nous voulions garder la persistance ignorante. Il s’agit d’un excellent exemple du type de compromis que vous devrez évaluer lors de la construction de code testable. Il ya des moments où vous avez besoin de laisser les préoccupations de persistance fuite en dehors de l’abstraction du référentiel pour atteindre les objectifs de performance.

L’alternative au chargement avide est le chargement paresseux. Le chargement paresseux signifie que nous n’avons *pas* besoin de notre code d’entreprise pour annoncer explicitement l’exigence pour les données associées. Au lieu de cela, nous utilisons nos entités dans l’application et si des données supplémentaires sont nécessaires Entity Framework chargera les données sur demande.

### <a name="lazy-loading"></a>Chargement différé

Il est facile d’imaginer un scénario où nous ne savons pas quelles données une logique d’entreprise aura besoin. Nous savons peut-être que la logique a besoin d’un objet de l’employé, mais nous pouvons nous brancher sur différentes voies d’exécution où certaines de ces voies exigent des informations de carte de temps de l’employé, et d’autres pas. Des scénarios comme celui-ci sont parfaits pour le chargement implicite paresseux parce que les données apparaissent comme par magie sur une base nécessaire.

Le chargement paresseux, également connu sous le nom de chargement différé, place certaines exigences sur nos objets d’entité. Les POCO avec une véritable ignorance de persistance ne seraient pas confrontés à des exigences de la couche de persistance, mais l’ignorance de la vraie persistance est pratiquement impossible à réaliser.Au lieu de cela, nous mesurons l’ignorance de persistance dans les degrés relatifs. Il serait regrettable que nous ayons besoin d’hériter d’une classe de base axée sur la persistance ou d’utiliser une collection spécialisée pour obtenir un chargement paresseux dans les POCO. Heureusement, EF4 a une solution moins intrusive.

### <a name="virtually-undetectable"></a>Pratiquement indétectable

Lors de l’utilisation d’objets POCO, EF4 peut générer dynamiquement des procurations en temps d’exécution pour les entités. Ces procurations enveloppent invisiblement les OPO matérialisées et fournissent des services supplémentaires en interceptant chaque propriété obtenir et définir l’opération pour effectuer des travaux supplémentaires. Un tel service est la fonction de chargement paresseux que nous recherchons. Un autre service est un mécanisme efficace de suivi des changements qui peut enregistrer lorsque le programme modifie la valeur des propriétés d’une entité. La liste des modifications est utilisée par l’ObjectContext pendant la méthode SaveChanges pour persister toutes les entités modifiées à l’aide de commandes UPDATE.

Pour que ces procurations fonctionnent, cependant, ils ont besoin d’un moyen de s’accrocher à la propriété obtenir et définir des opérations sur une entité, et les procurations atteindre cet objectif en dominant les membres virtuels. Ainsi, si nous voulons avoir un chargement implicite paresseux et un suivi efficace des changements, nous devons revenir à nos définitions de classe POCO et marquer les propriétés comme virtuelles.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Nous pouvons encore dire que l’entité employee est la plupart du temps la persistance ignorante. La seule exigence est d’utiliser des membres virtuels et cela n’a pas d’impact sur la testabilité du code. Nous n’avons pas besoin de dériver d’une classe de base spéciale, ou même utiliser une collection spéciale dédiée au chargement paresseux. Comme le code le démontre, toute&lt;classe&gt; implémentant ICollection T est disponible pour détenir des entités connexes.

Il y a aussi un changement mineur que nous devons apporter à l’intérieur de notre unité de travail. Le chargement paresseux est *éteint* par défaut lorsque vous travaillez directement avec un objet ObjectContext. Il y a une propriété que nous pouvons mettre sur la propriété ContextOptions pour permettre le chargement différé, et nous pouvons définir cette propriété à l’intérieur de notre unité de travail réelle si nous voulons permettre le chargement paresseux partout.

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

Avec le chargement paresseux implicite activé, le code d’application peut utiliser un employé et les cartes de temps associées de l’employé tout en restant parfaitement inconscient du travail requis pour EF pour charger les données supplémentaires.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Le chargement paresseux rend le code d’application plus facile à écrire, et avec la magie proxy le code reste complètement testable. Les faux en mémoire de l’unité de travail peuvent simplement précharger les fausses entités avec des données associées en cas de besoin lors d’un test.

À ce stade, nous allons tourner notre attention de&lt;la&gt; construction de dépôts en utilisant IObjectSet T et regarder les abstractions pour cacher tous les signes du cadre de persistance.

## <a name="custom-repositories"></a>Dépôts personnalisés

Lorsque nous avons présenté pour la première fois le modèle de conception de l’unité de travail dans cet article, nous avons fourni un certain code d’échantillon pour ce que l’unité de travail pourrait ressembler. Présentons à nouveau cette idée originale en utilisant le scénario de carte de temps de l’employé et des employés avec lequel nous avons travaillé.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La principale différence entre cette unité de travail et l’unité de travail que nous avons créée dans la dernière section est&lt;de&gt;savoir comment cette unité de travail n’utilise aucune abstraction du cadre EF4 (il n’y a pas d’IObjectSet T ). IObjectSet&lt;&gt; T fonctionne bien comme une interface de référentiel, mais l’API qu’il expose pourrait ne pas parfaitement correspondre aux besoins de notre application. Dans cette approche à venir, nous représenterons des&lt;&gt; dépôts à l’aide d’une abstraction IRepository T personnalisée.

De nombreux développeurs qui suivent la conception axée sur les tests, la conception&lt;&gt; axée sur le comportement, et la conception de méthodologies axées sur le domaine préfèrent l’approche IRepository T pour plusieurs raisons. Tout d’abord,&lt;l’interface IRepository T&gt; représente une couche « anti-corruption ». Comme décrit par Eric Evans dans son domain Driven Design livre une couche anti-corruption maintient votre code de domaine loin des API infrastructure, comme une API persistance. Deuxièmement, les développeurs peuvent intégrer des méthodes dans le référentiel qui répondent aux besoins exacts d’une application (comme découvert lors de la rédaction de tests). Par exemple, nous pourrions souvent avoir besoin de localiser une seule entité à l’aide d’une valeur d’identification, de sorte que nous pouvons ajouter une méthode FindById à l’interface de dépôt.Notre définition IRepository&lt;T&gt; ressemblera à ce qui suit.

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

Notez que nous allons revenir à&lt;l’utilisation d’une interface IQueryable T&gt; pour exposer les collections d’entités. IQueryable&lt;&gt; T permet aux arbres d’expression LINQ de s’écouler dans le fournisseur EF4 et de donner au fournisseur une vue holistique de la requête. Une deuxième option serait de retourner&lt;IEnumerable T&gt;, ce qui signifie que le fournisseur EF4 LINQ ne verra que les expressions construites à l’intérieur du dépôt. Tout regroupement, commande et projection effectué à l’extérieur du dépôt ne sera pas composé dans la commande SQL envoyée à la base de données, ce qui peut nuire aux performances. D’autre part, un référentiel de retour&lt;&gt; que les résultats IEnumerable T ne vous surprendra jamais avec une nouvelle commande SQL. Les deux approches fonctionneront, et les deux approches restent testables.

Il est simple de fournir une mise en&lt;&gt; œuvre unique de l’interface IRepository T à l’aide de génériques et de l’API EF4 ObjectContext.

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

L’approche IRepository&lt;T&gt; nous donne un contrôle supplémentaire sur nos requêtes parce qu’un client doit invoquer une méthode pour se rendre à une entité. À l’intérieur de la méthode, nous pourrions fournir des vérifications supplémentaires et des opérateurs de LINQ pour faire respecter les contraintes d’application. Notez que l’interface a deux contraintes sur le paramètre de type générique. La première contrainte est la classe contre&lt;souillée requise par ObjectSet T&gt;, et la deuxième contrainte oblige nos entités à mettre en œuvre IEntity - une abstraction créée pour l’application. L’interface IEntity oblige les entités à avoir une propriété Id lisible, et nous pouvons ensuite utiliser cette propriété dans la méthode FindById. IEntity est défini avec le code suivant.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity pourrait être considéré comme une petite violation de l’ignorance de persistance puisque nos entités sont tenues de mettre en œuvre cette interface. Rappelez-vous l’ignorance de persistance est sur les compromis, et pour beaucoup la fonctionnalité FindById l’emportera sur la contrainte imposée par l’interface. L’interface n’a aucun impact sur la testabilité.

L’instantanéisation d’un&lt;T&gt; IRepository en direct nécessite un EF4 ObjectContext, de sorte qu’une unité concrète de mise en œuvre du travail devrait gérer l’instantanéisation.

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

L’utilisation de notre référentiel personnalisé n’est pas significativement&lt;&gt;différente de l’utilisation du référentiel basé sur IObjectSet T . Au lieu d’appliquer directement les exploitants de LINQ à une propriété, nous devrons d’abord invoquer l’une des méthodes du référentiel pour saisir une référence IQueryable&lt;T.&gt;

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Remarquez que l’opérateur Personnalisé Inclure que nous avons mis en œuvre précédemment fonctionnera sans changement. La méthode FindById du référentiel supprime la logique dupliquée des actions visant à récupérer une seule entité.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Il n’y a pas de différence significative dans la testabilité des deux approches que nous avons examinées. Nous pourrions fournir de fausses implémentations d’IRepository&lt;T&gt; en construisant des classes en béton soutenues par HashSet&lt;Employee&gt; - tout comme ce que nous avons fait dans la dernière section. Cependant, certains développeurs préfèrent utiliser des objets fictifs et des cadres d’objets simulés au lieu de construire des faux. Nous allons envisager d’utiliser des maquettes pour tester notre implémentation et discuter des différences entre les moqueurs et les faux dans la section suivante.

### <a name="testing-with-mocks"></a>Test avec Mocks

Il existe différentes approches pour construire ce que Martin Fowler appelle un « double test ». Un double test (comme un double film cascadeur) est un objet que vous construisez pour "stand in" pour de vrais objets de production pendant les tests. Les dépôts en mémoire que nous avons créés sont des doubles de test pour les référentiels qui parlent à SQL Server. Nous avons vu comment utiliser ces tests-doubles pendant les tests unitaires pour isoler le code et faire fonctionner les tests rapidement.

Les doubles de test que nous avons construits ont de véritables implémentations de travail. Dans les coulisses, chacun stocke une collection d’objets en béton, et ils ajouteront et supprimeront des objets de cette collection lorsque nous manipulons le référentiel lors d’un test. Certains développeurs aiment construire leur test double de cette façon - avec un code réel et des implémentations de travail.Ces doubles test sont ce que nous appelons *des faux*. Ils ont des implémentations de travail, mais ils ne sont pas assez réels pour l’utilisation de la production. Le faux référentiel n’écrit pas à la base de données. Le faux serveur SMTP n’envoie pas de message électronique sur le réseau.

### <a name="mocks-versus-fakes"></a>Mocks contre Fakes

Il ya un autre type de double test connu sous le nom d’un *simulacre*. Alors que les faux ont des implémentations de travail, les moqueries viennent sans implémentation. À l’aide d’un cadre d’objets fictifs, nous construisons ces objets simulés au moment de l’exécution et les utilisons comme doubles d’essai. Dans cette section, nous allons utiliser le cadre de moquerie open source Moq. Voici un exemple simple d’utilisation de Moq pour créer dynamiquement un double test pour un référentiel d’employé.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Nous demandons à Moq une&lt;&gt; mise en œuvre de l’IRepository Employee et elle en construit une dynamique. Nous pouvons accéder à l’objet&lt;de&gt; mise en œuvre&lt;IRepository Employee en accédant à la propriété Objet de l’objet Mock T.&gt; C’est cet objet intérieur que nous pouvons transmettre à nos contrôleurs, et ils ne sauront pas s’il s’agit d’un double test ou du véritable référentiel. Nous pouvons invoquer des méthodes sur l’objet tout comme nous invoquons des méthodes sur un objet avec une réelle implémentation.

Vous devez vous demander ce que le faux référentiel fera lorsque nous invoquons la méthode Add. Comme il n’y a pas d’implémentation derrière l’objet simulé, Add ne fait rien. Il n’y a pas de collection de béton dans les coulisses comme nous l’avions eu avec les faux que nous avons écrits, de sorte que l’employé est jeté. Qu’en est-il de la valeur de retour de FindById? Dans ce cas, l’objet simulé fait la seule chose qu’il peut faire, qui est de retourner une valeur par défaut. Puisque nous revenons à un type de référence (un employé), la valeur de rendement est une valeur nulle.

Les faux peuvent sembler sans valeur; cependant, il ya deux autres caractéristiques de moqueries que nous n’avons pas parlé. Tout d’abord, le cadre Moq enregistre tous les appels effectués sur l’objet simulé. Plus tard dans le code, nous pouvons demander à Moq si quelqu’un a invoqué la méthode Add, ou si quelqu’un a invoqué la méthode FindById. Nous verrons plus tard comment nous pouvons utiliser cette fonction d’enregistrement "boîte noire" dans les tests.

La deuxième grande fonctionnalité est de savoir comment nous pouvons utiliser Moq pour programmer un objet simulé avec *des attentes*. Une attente indique à l’objet simulé comment répondre à une interaction donnée. Par exemple, nous pouvons programmer une attente dans notre maquette et lui dire de retourner un objet employé quand quelqu’un invoque FindById. Le cadre Moq utilise une API de configuration et des expressions lambda pour programmer ces attentes.

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

Dans cet échantillon, nous demandons à Moq de construire dynamiquement un référentiel, puis nous programmons le référentiel avec une attente. L’attente indique à l’objet simulé de retourner un nouvel objet employé d’une valeur Id de 5 lorsque quelqu’un invoque la méthode FindById en passant une valeur de 5. Ce test passe, et nous n’avons pas besoin de&lt;construire&gt;une implémentation complète pour faux IRepository T .

Revenons sur les tests que nous avons écrits plus tôt et retravainons-les pour utiliser des maquettes au lieu de faux. Comme avant, nous allons utiliser une classe de base pour configurer les pièces communes de l’infrastructure dont nous avons besoin pour tous les tests du contrôleur.

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

Le code de configuration reste la plupart du temps le même. Au lieu d’utiliser des faux, nous allons utiliser Moq pour construire des objets simulés. La classe de base s’arrange pour que l’unité de travail fictive renvoie un faux référentiel lorsque le code invoque la propriété des employés. Le reste de la configuration simulée aura lieu à l’intérieur des montages d’essai dédiés à chaque scénario spécifique. Par exemple, le montage de test pour l’action Index configurera le faux référentiel pour renvoyer une liste d’employés lorsque l’action invoque la méthode FindAll du faux référentiel.

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

À l’exception des attentes, nos tests ressemblent aux tests que nous avions auparavant. Cependant, avec la capacité d’enregistrement d’un cadre fictif, nous pouvons aborder les tests sous un angle différent. Nous examinerons cette nouvelle perspective dans la section suivante.

### <a name="state-versus-interaction-testing"></a>Test d’état contre interaction

Il existe différentes techniques que vous pouvez utiliser pour tester un logiciel avec des objets simulés. Une approche consiste à utiliser les tests basés sur l’État, ce que nous avons fait dans ce document jusqu’à présent. Les tests basés sur l’État font des affirmations sur l’état du logiciel. Dans le dernier test, nous avons invoqué une méthode d’action sur le contrôleur et fait une affirmation sur le modèle qu’il devrait construire. Voici quelques autres exemples d’état de test :

-   Vérifier le référentiel contient le nouvel objet de l’employé après l’exécution de Create.
-   Vérifier le modèle contient une liste de tous les employés après index exécute.
-   Vérifier le référentiel ne contient pas un employé donné après l’exécution de Suppression.

Une autre approche que vous verrez avec des objets simulés est de vérifier les *interactions*. Alors que les tests basés sur l’état font des affirmations sur l’état des objets, les tests basés sur l’interaction font des affirmations sur la façon dont les objets interagissent. Par exemple :

-   Vérifiez que le contrôleur invoque la méthode Add du référentiel lorsque La création s’exécute.
-   Vérifiez que le contrôleur invoque la méthode FindAll du référentiel lorsque Index s’exécute.
-   Vérifiez que le contrôleur invoque la méthode Commit de l’unité de travail pour enregistrer les modifications lorsque Edit s’exécute.

Les tests d’interaction nécessitent souvent moins de données de test, car nous ne pions pas à l’intérieur des collections et vérifions les comptes. Par exemple, si nous savons que l’action Détails invoque la méthode FindById d’un référentiel avec la valeur correcte - alors l’action se comporte probablement correctement. Nous pouvons vérifier ce comportement sans configurer les données de test à revenir de FindById.

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

La seule configuration requise dans le montage de test ci-dessus est la configuration fournie par la classe de base. Lorsque nous invoquons l’action du contrôleur, Moq enregistre les interactions avec le faux référentiel. À l’aide de l’API Verify de Moq, nous pouvons demander à Moq si le contrôleur a invoqué FindById avec la valeur d’identification appropriée. Si le contrôleur n’a pas invoqué la méthode, ou invoqué la méthode avec une valeur de paramètre inattendue, la méthode Vérifier lancera une exception et le test échouera.

Voici un autre exemple pour vérifier l’action Créer invoque Commit sur l’unité de travail actuelle.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Un danger avec les tests d’interaction est la tendance à surcifier les interactions. La capacité de l’objet simulé d’enregistrer et de vérifier chaque interaction avec l’objet simulé ne signifie pas que le test doit essayer de vérifier chaque interaction. Certaines interactions sont des détails d’implémentation et vous ne devez vérifier que les interactions *requises* pour satisfaire le test actuel.

Le choix entre les maquettes ou les faux dépend en grande partie du système que vous testez et de vos préférences personnelles (ou d’équipe). Les objets simulés peuvent réduire considérablement la quantité de code dont vous avez besoin pour implémenter des doubles tests, mais tout le monde n’est pas à l’aise d’attentes de programmation et de vérification des interactions.

## <a name="conclusions"></a>Conclusions

Dans cet article, nous avons démontré plusieurs approches pour créer du code testable tout en utilisant le cadre d’entité ADO.NET pour la persistance des données. Nous pouvons tirer parti construit dans&lt;des&gt;abstractions comme IObjectSet T&lt;&gt;, ou créer nos propres abstractions comme IRepository T .Dans les deux cas, le soutien du POCO dans le cadre d’entités ADO.NET 4.0 permet aux consommateurs de ces abstractions de rester ignorants persistants et hautement testables. D’autres fonctionnalités EF4 comme le chargement paresseux implicite permettent au code de service d’entreprise et d’application de fonctionner sans se soucier des détails d’un magasin de données relationnelles. Enfin, les abstractions que nous créons sont faciles à se moquer ou à simuler à l’intérieur des tests unitaires, et nous pouvons utiliser ces doubles de test pour réaliser des tests rapides, très isolés et fiables.

### <a name="additional-resources"></a>Ressources supplémentaires

-   Robert C. Martin, « [Le principe de la responsabilité unique](https://www.objectmentor.com/resources/articles/srp.pdf)»
-   Martin Fowler, [Catalogue des modèles](https://www.martinfowler.com/eaaCatalog/index.html) de Patterns of Enterprise Application *Architecture*
-   Griffin Caprio, " [Injection de dépendance](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog de programmabilité des données, « [Procédure pas à pas : Développement piloté par test avec le cadre d’entité 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)».
-   Blog de programmabilité des données, « [Utilisation du dépôt et des modèles d’unité de travail avec le cadre d’entité 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)»
-   Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, " [BDD avec MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, " [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biographie

Scott Allen est membre du personnel technique de Pluralsight et fondateur de OdeToCode.com. En 15 ans de développement de logiciels commerciaux, Scott a travaillé sur des solutions pour tout, des appareils embarqués 8 bits aux applications Web ASP.NET hautement évolutives. Vous pouvez joindre Scott sur son blog à [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode, ou sur Twitter à .
