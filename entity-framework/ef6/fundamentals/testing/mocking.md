---
title: Test avec un Framework fictif-EF6
description: Test avec une infrastructure fictive dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 01890ab3bb8dbf0caa7b3eff797e53b06bc8ec9b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618299"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="496f7-103">Test avec un Framework fictif</span><span class="sxs-lookup"><span data-stu-id="496f7-103">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="496f7-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="496f7-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="496f7-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="496f7-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="496f7-106">Lors de l’écriture de tests pour votre application, il est souvent préférable d’éviter d’atteindre la base de données.</span><span class="sxs-lookup"><span data-stu-id="496f7-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="496f7-107">Entity Framework vous permet d’y parvenir en créant un contexte, avec un comportement défini par vos tests, qui utilise des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="496f7-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="496f7-108">Options de création de doubles de test</span><span class="sxs-lookup"><span data-stu-id="496f7-108">Options for creating test doubles</span></span>  

<span data-ttu-id="496f7-109">Il existe deux approches différentes qui peuvent être utilisées pour créer une version en mémoire de votre contexte.</span><span class="sxs-lookup"><span data-stu-id="496f7-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="496f7-110">**Créer vos propres doubles de test** : cette approche implique l’écriture de votre propre implémentation en mémoire de votre contexte et DbSets.</span><span class="sxs-lookup"><span data-stu-id="496f7-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="496f7-111">Cela vous donne un grand contrôle sur la façon dont les classes se comportent, mais peut impliquer l’écriture et la possession d’une quantité raisonnable de code.</span><span class="sxs-lookup"><span data-stu-id="496f7-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="496f7-112">**Utilisez une infrastructure fictive pour créer des doubles de test** : à l’aide d’une infrastructure fictive (telle que MOQ), vous pouvez avoir les implémentations en mémoire de votre contexte et les jeux créés dynamiquement au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="496f7-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="496f7-113">Cet article traite de l’utilisation d’un Framework fictif.</span><span class="sxs-lookup"><span data-stu-id="496f7-113">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="496f7-114">Pour créer vos propres doubles de test, consultez [test avec vos propres doubles de test](xref:ef6/fundamentals/testing/writing-test-doubles).</span><span class="sxs-lookup"><span data-stu-id="496f7-114">For creating your own test doubles see [Testing with Your Own Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span></span>  

<span data-ttu-id="496f7-115">Pour illustrer l’utilisation d’EF avec une infrastructure fictive, nous allons utiliser MOQ.</span><span class="sxs-lookup"><span data-stu-id="496f7-115">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="496f7-116">Le moyen le plus simple d’utiliser MOQ consiste à installer le [package MOQ à partir de NuGet](https://nuget.org/packages/Moq/).</span><span class="sxs-lookup"><span data-stu-id="496f7-116">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="496f7-117">Test avec les versions antérieures à EF6</span><span class="sxs-lookup"><span data-stu-id="496f7-117">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="496f7-118">Le scénario présenté dans cet article dépend de certaines modifications apportées à DbSet dans EF6.</span><span class="sxs-lookup"><span data-stu-id="496f7-118">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="496f7-119">Pour tester avec EF5 et les versions antérieures, consultez [test avec un contexte factice](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="496f7-119">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="496f7-120">Limitations des doubles de test en mémoire EF</span><span class="sxs-lookup"><span data-stu-id="496f7-120">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="496f7-121">Les doubles de test en mémoire peuvent être un bon moyen de fournir une couverture de niveau test unitaire des bits de votre application qui utilisent EF.</span><span class="sxs-lookup"><span data-stu-id="496f7-121">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="496f7-122">Toutefois, lorsque vous procédez ainsi, vous utilisez LINQ to Objects pour exécuter des requêtes sur des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="496f7-122">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="496f7-123">Cela peut entraîner un comportement différent de celui de l’utilisation du fournisseur LINQ d’EF (LINQ to Entities) pour convertir les requêtes en SQL exécuté sur votre base de données.</span><span class="sxs-lookup"><span data-stu-id="496f7-123">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="496f7-124">Le chargement de données associées est un exemple de cette différence.</span><span class="sxs-lookup"><span data-stu-id="496f7-124">One example of such a difference is loading related data.</span></span> <span data-ttu-id="496f7-125">Si vous créez une série de blogs qui ont chacun des publications associées, lorsque vous utilisez des données en mémoire, les publications associées sont toujours chargées pour chaque blog.</span><span class="sxs-lookup"><span data-stu-id="496f7-125">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="496f7-126">Toutefois, lors de l’exécution sur une base de données, les données sont chargées uniquement si vous utilisez la méthode Include.</span><span class="sxs-lookup"><span data-stu-id="496f7-126">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="496f7-127">Pour cette raison, il est recommandé de toujours inclure un certain niveau de test de bout en bout (en plus de vos tests unitaires) pour garantir le bon fonctionnement de votre application sur une base de données.</span><span class="sxs-lookup"><span data-stu-id="496f7-127">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="496f7-128">Suivre les étapes de cet article</span><span class="sxs-lookup"><span data-stu-id="496f7-128">Following along with this article</span></span>  

<span data-ttu-id="496f7-129">Cet article fournit des listes de code complètes que vous pouvez copier dans Visual Studio pour suivre la procédure si vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="496f7-129">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="496f7-130">Il est plus facile de créer un **projet de test unitaire** et vous devrez cibler **.NET Framework 4,5** pour terminer les sections qui utilisent Async.</span><span class="sxs-lookup"><span data-stu-id="496f7-130">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="496f7-131">Le modèle EF</span><span class="sxs-lookup"><span data-stu-id="496f7-131">The EF model</span></span>  

<span data-ttu-id="496f7-132">Le service que nous allons tester utilise un modèle EF constitué du BloggingContext et du blog et des classes de publication.</span><span class="sxs-lookup"><span data-stu-id="496f7-132">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="496f7-133">Ce code peut avoir été généré par le concepteur EF ou être un modèle de Code First.</span><span class="sxs-lookup"><span data-stu-id="496f7-133">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="496f7-134">Propriétés DbSet virtuelles avec le concepteur EF</span><span class="sxs-lookup"><span data-stu-id="496f7-134">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="496f7-135">Notez que les propriétés DbSet sur le contexte sont marquées comme virtuelles.</span><span class="sxs-lookup"><span data-stu-id="496f7-135">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="496f7-136">Cela permettra à l’infrastructure factice de dériver de notre contexte et de remplacer ces propriétés par une implémentation fictive.</span><span class="sxs-lookup"><span data-stu-id="496f7-136">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="496f7-137">Si vous utilisez Code First vous pouvez modifier vos classes directement.</span><span class="sxs-lookup"><span data-stu-id="496f7-137">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="496f7-138">Si vous utilisez le concepteur EF, vous devez modifier le modèle T4 qui génère votre contexte.</span><span class="sxs-lookup"><span data-stu-id="496f7-138">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="496f7-139">Ouvrez le \<model_name\> . Fichier Context.tt imbriqué sous votre fichier edmx, recherchez le fragment de code suivant et ajoutez le mot clé Virtual comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="496f7-139">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="496f7-140">Service à tester</span><span class="sxs-lookup"><span data-stu-id="496f7-140">Service to be tested</span></span>  

<span data-ttu-id="496f7-141">Pour illustrer le test avec des doubles de test en mémoire, nous allons écrire deux tests pour un BlogService.</span><span class="sxs-lookup"><span data-stu-id="496f7-141">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="496f7-142">Le service est en charge de la création de nouveaux blogs (AddBlog) et de la restauration de tous les blogs classés par nom (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="496f7-142">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="496f7-143">En plus de GetAllBlogs, nous avons également fourni une méthode qui permet d’obtenir de façon asynchrone tous les blogs classés par nom (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="496f7-143">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="496f7-144">Test des scénarios non-requête</span><span class="sxs-lookup"><span data-stu-id="496f7-144">Testing non-query scenarios</span></span>  

<span data-ttu-id="496f7-145">C’est tout ce dont nous avons besoin pour commencer à tester des méthodes qui ne sont pas des requêtes.</span><span class="sxs-lookup"><span data-stu-id="496f7-145">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="496f7-146">Le test suivant utilise MOQ pour créer un contexte.</span><span class="sxs-lookup"><span data-stu-id="496f7-146">The following test uses Moq to create a context.</span></span> <span data-ttu-id="496f7-147">Il crée ensuite un DbSet \<Blog\> et le lie à la propriété blogs du contexte.</span><span class="sxs-lookup"><span data-stu-id="496f7-147">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="496f7-148">Ensuite, le contexte est utilisé pour créer un nouveau BlogService qui est ensuite utilisé pour créer un nouveau blog, à l’aide de la méthode AddBlog.</span><span class="sxs-lookup"><span data-stu-id="496f7-148">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="496f7-149">Enfin, le test vérifie que le service a ajouté un nouveau blog et a appelé SaveChanges dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="496f7-149">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a><span data-ttu-id="496f7-150">Test des scénarios de requête</span><span class="sxs-lookup"><span data-stu-id="496f7-150">Testing query scenarios</span></span>  

<span data-ttu-id="496f7-151">Pour pouvoir exécuter des requêtes sur notre double de test DbSet, nous devons configurer une implémentation de IQueryable.</span><span class="sxs-lookup"><span data-stu-id="496f7-151">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="496f7-152">La première étape consiste à créer des données en mémoire : nous utilisons une liste \<Blog\> .</span><span class="sxs-lookup"><span data-stu-id="496f7-152">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="496f7-153">Ensuite, nous créons un contexte et DBSet, puis nous mettons \<Blog\> en place l’implémentation IQueryable pour le DBSet : elles se délèguent simplement au fournisseur LINQ to Objects qui fonctionne avec la liste \<T\> .</span><span class="sxs-lookup"><span data-stu-id="496f7-153">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="496f7-154">Nous pouvons ensuite créer un BlogService basé sur nos doubles de test et vous assurer que les données que nous obtenons de GetAllBlogs sont classées par nom.</span><span class="sxs-lookup"><span data-stu-id="496f7-154">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a><span data-ttu-id="496f7-155">Test avec des requêtes Async</span><span class="sxs-lookup"><span data-stu-id="496f7-155">Testing with async queries</span></span>

<span data-ttu-id="496f7-156">Entity Framework 6 a introduit un ensemble de méthodes d’extension qui peuvent être utilisées pour exécuter une requête de manière asynchrone.</span><span class="sxs-lookup"><span data-stu-id="496f7-156">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="496f7-157">ToListAsync, FirstAsync, ForEachAsync, etc. sont des exemples de ces méthodes.</span><span class="sxs-lookup"><span data-stu-id="496f7-157">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="496f7-158">Étant donné que les requêtes de Entity Framework utilisent LINQ, les méthodes d’extension sont définies sur IQueryable et IEnumerable.</span><span class="sxs-lookup"><span data-stu-id="496f7-158">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="496f7-159">Toutefois, étant donné qu’elles sont uniquement conçues pour être utilisées avec Entity Framework vous pouvez recevoir l’erreur suivante si vous essayez de les utiliser sur une requête LINQ qui n’est pas une requête Entity Framework :</span><span class="sxs-lookup"><span data-stu-id="496f7-159">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="496f7-160">L’IQueryable source n’implémente pas IDbAsyncEnumerable {0} .</span><span class="sxs-lookup"><span data-stu-id="496f7-160">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="496f7-161">Seules les sources qui implémentent IDbAsyncEnumerable peuvent être utilisées pour Entity Framework opérations asynchrones.</span><span class="sxs-lookup"><span data-stu-id="496f7-161">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="496f7-162">Pour plus d’informations [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) , consultez.</span><span class="sxs-lookup"><span data-stu-id="496f7-162">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="496f7-163">Alors que les méthodes Async sont uniquement prises en charge lors d’une exécution sur une requête EF, vous pouvez les utiliser dans votre test unitaire en cas d’exécution sur un double de test en mémoire d’un DbSet.</span><span class="sxs-lookup"><span data-stu-id="496f7-163">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="496f7-164">Pour pouvoir utiliser les méthodes Async, nous devons créer un DbAsyncQueryProvider en mémoire pour traiter la requête asynchrone.</span><span class="sxs-lookup"><span data-stu-id="496f7-164">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="496f7-165">Bien qu’il soit possible de configurer un fournisseur de requêtes à l’aide de MOQ, il est beaucoup plus facile de créer une implémentation de double de test dans le code.</span><span class="sxs-lookup"><span data-stu-id="496f7-165">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="496f7-166">Le code pour cette implémentation est le suivant :</span><span class="sxs-lookup"><span data-stu-id="496f7-166">The code for this implementation is as follows:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
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

<span data-ttu-id="496f7-167">Maintenant que nous disposons d’un fournisseur de requêtes asynchrones, nous pouvons écrire un test unitaire pour notre nouvelle méthode GetAllBlogsAsync.</span><span class="sxs-lookup"><span data-stu-id="496f7-167">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
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

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
