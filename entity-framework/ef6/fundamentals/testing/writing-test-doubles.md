---
title: Test avec vos propres doubles de test-EF6
description: Test avec vos propres doubles de test dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 5c8e50247c0e6ed4081e2403ba60e0738051312b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618239"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="46327-103">Test avec vos propres doubles de test</span><span class="sxs-lookup"><span data-stu-id="46327-103">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="46327-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="46327-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="46327-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="46327-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="46327-106">Lors de l’écriture de tests pour votre application, il est souvent préférable d’éviter d’atteindre la base de données.</span><span class="sxs-lookup"><span data-stu-id="46327-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="46327-107">Entity Framework vous permet d’y parvenir en créant un contexte, avec un comportement défini par vos tests, qui utilise des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="46327-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="46327-108">Options de création de doubles de test</span><span class="sxs-lookup"><span data-stu-id="46327-108">Options for creating test doubles</span></span>  

<span data-ttu-id="46327-109">Il existe deux approches différentes qui peuvent être utilisées pour créer une version en mémoire de votre contexte.</span><span class="sxs-lookup"><span data-stu-id="46327-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="46327-110">**Créer vos propres doubles de test** : cette approche implique l’écriture de votre propre implémentation en mémoire de votre contexte et DbSets.</span><span class="sxs-lookup"><span data-stu-id="46327-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="46327-111">Cela vous donne un grand contrôle sur la façon dont les classes se comportent, mais peut impliquer l’écriture et la possession d’une quantité raisonnable de code.</span><span class="sxs-lookup"><span data-stu-id="46327-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="46327-112">**Utilisez une infrastructure fictive pour créer des doubles de test** : à l’aide d’une infrastructure fictive (telle que MOQ), vous pouvez avoir les implémentations en mémoire de votre contexte et les jeux créés dynamiquement au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="46327-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="46327-113">Cet article traite de la création de votre propre double de test.</span><span class="sxs-lookup"><span data-stu-id="46327-113">This article will deal with creating your own test double.</span></span> <span data-ttu-id="46327-114">Pour plus d’informations sur l’utilisation d’une infrastructure fictive, consultez [test avec un Framework fictif](xref:ef6/fundamentals/testing/mocking).</span><span class="sxs-lookup"><span data-stu-id="46327-114">For information on using a mocking framework see [Testing with a Mocking Framework](xref:ef6/fundamentals/testing/mocking).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="46327-115">Test avec les versions antérieures à EF6</span><span class="sxs-lookup"><span data-stu-id="46327-115">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="46327-116">Le code présenté dans cet article est compatible avec EF6.</span><span class="sxs-lookup"><span data-stu-id="46327-116">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="46327-117">Pour tester avec EF5 et les versions antérieures, consultez [test avec un contexte factice](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="46327-117">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="46327-118">Limitations des doubles de test en mémoire EF</span><span class="sxs-lookup"><span data-stu-id="46327-118">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="46327-119">Les doubles de test en mémoire peuvent être un bon moyen de fournir une couverture de niveau test unitaire des bits de votre application qui utilisent EF.</span><span class="sxs-lookup"><span data-stu-id="46327-119">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="46327-120">Toutefois, lorsque vous procédez ainsi, vous utilisez LINQ to Objects pour exécuter des requêtes sur des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="46327-120">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="46327-121">Cela peut entraîner un comportement différent de celui de l’utilisation du fournisseur LINQ d’EF (LINQ to Entities) pour convertir les requêtes en SQL exécuté sur votre base de données.</span><span class="sxs-lookup"><span data-stu-id="46327-121">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="46327-122">Le chargement de données associées est un exemple de cette différence.</span><span class="sxs-lookup"><span data-stu-id="46327-122">One example of such a difference is loading related data.</span></span> <span data-ttu-id="46327-123">Si vous créez une série de blogs qui ont chacun des publications associées, lorsque vous utilisez des données en mémoire, les publications associées sont toujours chargées pour chaque blog.</span><span class="sxs-lookup"><span data-stu-id="46327-123">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="46327-124">Toutefois, lors de l’exécution sur une base de données, les données sont chargées uniquement si vous utilisez la méthode Include.</span><span class="sxs-lookup"><span data-stu-id="46327-124">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="46327-125">Pour cette raison, il est recommandé de toujours inclure un certain niveau de test de bout en bout (en plus de vos tests unitaires) pour garantir le bon fonctionnement de votre application sur une base de données.</span><span class="sxs-lookup"><span data-stu-id="46327-125">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="46327-126">Suivre les étapes de cet article</span><span class="sxs-lookup"><span data-stu-id="46327-126">Following along with this article</span></span>  

<span data-ttu-id="46327-127">Cet article fournit des listes de code complètes que vous pouvez copier dans Visual Studio pour suivre la procédure si vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="46327-127">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="46327-128">Il est plus facile de créer un **projet de test unitaire** et vous devrez cibler **.NET Framework 4,5** pour terminer les sections qui utilisent Async.</span><span class="sxs-lookup"><span data-stu-id="46327-128">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="46327-129">Création d’une interface de contexte</span><span class="sxs-lookup"><span data-stu-id="46327-129">Creating a context interface</span></span>  

<span data-ttu-id="46327-130">Nous allons examiner le test d’un service qui utilise un modèle EF.</span><span class="sxs-lookup"><span data-stu-id="46327-130">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="46327-131">Pour pouvoir remplacer notre contexte EF par une version en mémoire à des fins de test, nous allons définir une interface qui sera implémentée par notre contexte EF (et c’est en mémoire double).</span><span class="sxs-lookup"><span data-stu-id="46327-131">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="46327-132">Le service que nous allons tester interroge et modifie les données à l’aide des propriétés DbSet de notre contexte et appelle également SaveChanges pour envoyer les modifications à la base de données.</span><span class="sxs-lookup"><span data-stu-id="46327-132">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="46327-133">Nous incluons donc ces membres sur l’interface.</span><span class="sxs-lookup"><span data-stu-id="46327-133">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="46327-134">Le modèle EF</span><span class="sxs-lookup"><span data-stu-id="46327-134">The EF model</span></span>  

<span data-ttu-id="46327-135">Le service que nous allons tester utilise un modèle EF constitué du BloggingContext et du blog et des classes de publication.</span><span class="sxs-lookup"><span data-stu-id="46327-135">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="46327-136">Ce code peut avoir été généré par le concepteur EF ou être un modèle de Code First.</span><span class="sxs-lookup"><span data-stu-id="46327-136">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="46327-137">Implémentation de l’interface de contexte à l’aide du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="46327-137">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="46327-138">Notez que notre contexte implémente l’interface IBloggingContext.</span><span class="sxs-lookup"><span data-stu-id="46327-138">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="46327-139">Si vous utilisez Code First vous pouvez modifier votre contexte directement pour implémenter l’interface.</span><span class="sxs-lookup"><span data-stu-id="46327-139">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="46327-140">Si vous utilisez le concepteur EF, vous devez modifier le modèle T4 qui génère votre contexte.</span><span class="sxs-lookup"><span data-stu-id="46327-140">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="46327-141">Ouvrez le \<model_name\> . Fichier Context.tt imbriqué sous votre fichier edmx, recherchez le fragment de code suivant et ajoutez-le dans l’interface, comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="46327-141">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="46327-142">Service à tester</span><span class="sxs-lookup"><span data-stu-id="46327-142">Service to be tested</span></span>  

<span data-ttu-id="46327-143">Pour illustrer le test avec des doubles de test en mémoire, nous allons écrire deux tests pour un BlogService.</span><span class="sxs-lookup"><span data-stu-id="46327-143">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="46327-144">Le service est en charge de la création de nouveaux blogs (AddBlog) et de la restauration de tous les blogs classés par nom (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="46327-144">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="46327-145">En plus de GetAllBlogs, nous avons également fourni une méthode qui permet d’obtenir de façon asynchrone tous les blogs classés par nom (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="46327-145">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="46327-146">Création des doubles de test en mémoire</span><span class="sxs-lookup"><span data-stu-id="46327-146">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="46327-147">Maintenant que nous disposons du véritable modèle EF et du service qui peut l’utiliser, il est temps de créer le double de test en mémoire que nous pouvons utiliser pour le test.</span><span class="sxs-lookup"><span data-stu-id="46327-147">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="46327-148">Nous avons créé un double de test TestContext pour notre contexte.</span><span class="sxs-lookup"><span data-stu-id="46327-148">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="46327-149">Dans les doubles de test, nous avons choisi le comportement que nous souhaitons pour prendre en charge les tests que nous allons exécuter.</span><span class="sxs-lookup"><span data-stu-id="46327-149">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="46327-150">Dans cet exemple, nous capturons simplement le nombre de fois que SaveChanges est appelé, mais vous pouvez inclure la logique nécessaire pour vérifier le scénario que vous testez.</span><span class="sxs-lookup"><span data-stu-id="46327-150">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="46327-151">Nous avons également créé un TestDbSet qui fournit une implémentation en mémoire de DbSet.</span><span class="sxs-lookup"><span data-stu-id="46327-151">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="46327-152">Nous avons fourni une implémentation complète pour toutes les méthodes sur DbSet (à l’exception de Find), mais vous devez uniquement implémenter les membres que votre scénario de test utilisera.</span><span class="sxs-lookup"><span data-stu-id="46327-152">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="46327-153">TestDbSet utilise d’autres classes d’infrastructure que nous avons incluses pour s’assurer que les requêtes asynchrones peuvent être traitées.</span><span class="sxs-lookup"><span data-stu-id="46327-153">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

### <a name="implementing-find"></a><span data-ttu-id="46327-154">Implémentation de Find</span><span class="sxs-lookup"><span data-stu-id="46327-154">Implementing Find</span></span>  

<span data-ttu-id="46327-155">La méthode Find est difficile à implémenter de manière générique.</span><span class="sxs-lookup"><span data-stu-id="46327-155">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="46327-156">Si vous devez tester du code qui utilise la méthode Find, il est plus facile de créer un DbSet de test pour chacun des types d’entité devant prendre en charge Find.</span><span class="sxs-lookup"><span data-stu-id="46327-156">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="46327-157">Vous pouvez ensuite écrire une logique pour rechercher ce type particulier d’entité, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="46327-157">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="46327-158">Écrire des tests</span><span class="sxs-lookup"><span data-stu-id="46327-158">Writing some tests</span></span>  

<span data-ttu-id="46327-159">C’est tout ce dont nous avons besoin pour commencer le test.</span><span class="sxs-lookup"><span data-stu-id="46327-159">That’s all we need to do to start testing.</span></span> <span data-ttu-id="46327-160">Le test suivant crée un TestContext, puis un service basé sur ce contexte.</span><span class="sxs-lookup"><span data-stu-id="46327-160">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="46327-161">Le service est ensuite utilisé pour créer un blog, à l’aide de la méthode AddBlog.</span><span class="sxs-lookup"><span data-stu-id="46327-161">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="46327-162">Enfin, le test vérifie que le service a ajouté un nouveau blog à la propriété blogs du contexte et a appelé SaveChanges sur le contexte.</span><span class="sxs-lookup"><span data-stu-id="46327-162">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="46327-163">Il s’agit simplement d’un exemple des types de choses que vous pouvez tester avec un double de test en mémoire et vous pouvez ajuster la logique des doubles de test et la vérification pour répondre à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="46327-163">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="46327-164">Voici un autre exemple de test : cette fois-ci, il exécute une requête.</span><span class="sxs-lookup"><span data-stu-id="46327-164">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="46327-165">Le test commence par la création d’un contexte de test avec des données dans sa propriété de blog. Notez que les données ne sont pas classées par ordre alphabétique.</span><span class="sxs-lookup"><span data-stu-id="46327-165">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="46327-166">Nous pouvons ensuite créer un BlogService basé sur notre contexte de test et vous assurer que les données renvoyées par GetAllBlogs sont classées par nom.</span><span class="sxs-lookup"><span data-stu-id="46327-166">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="46327-167">Enfin, nous allons écrire un test supplémentaire qui utilise notre méthode Async pour s’assurer que l’infrastructure Async que nous avons incluse dans [TestDbSet](#creating-the-in-memory-test-doubles) fonctionne.</span><span class="sxs-lookup"><span data-stu-id="46327-167">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
