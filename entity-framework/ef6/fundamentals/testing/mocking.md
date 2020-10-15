---
title: Test avec un Framework fictif-EF6
description: Test avec une infrastructure fictive dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 893d9f921adc148465830dee5dbfebca4d4c8f50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062917"
---
# <a name="testing-with-a-mocking-framework"></a>Test avec un Framework fictif
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Lors de l’écriture de tests pour votre application, il est souvent préférable d’éviter d’atteindre la base de données.  Entity Framework vous permet d’y parvenir en créant un contexte, avec un comportement défini par vos tests, qui utilise des données en mémoire.  

## <a name="options-for-creating-test-doubles"></a>Options de création de doubles de test  

Il existe deux approches différentes qui peuvent être utilisées pour créer une version en mémoire de votre contexte.  

- **Créer vos propres doubles de test** : cette approche implique l’écriture de votre propre implémentation en mémoire de votre contexte et DbSets. Cela vous donne un grand contrôle sur la façon dont les classes se comportent, mais peut impliquer l’écriture et la possession d’une quantité raisonnable de code.  
- **Utilisez une infrastructure fictive pour créer des doubles de test** : à l’aide d’une infrastructure fictive (telle que MOQ), vous pouvez avoir les implémentations en mémoire de votre contexte et les jeux créés dynamiquement au moment de l’exécution.  

Cet article traite de l’utilisation d’un Framework fictif. Pour créer vos propres doubles de test, consultez [test avec vos propres doubles de test](xref:ef6/fundamentals/testing/writing-test-doubles).  

Pour illustrer l’utilisation d’EF avec une infrastructure fictive, nous allons utiliser MOQ. Le moyen le plus simple d’utiliser MOQ consiste à installer le [package MOQ à partir de NuGet](https://nuget.org/packages/Moq/).  

## <a name="testing-with-pre-ef6-versions"></a>Test avec les versions antérieures à EF6  

Le scénario présenté dans cet article dépend de certaines modifications apportées à DbSet dans EF6. Pour tester avec EF5 et les versions antérieures, consultez [test avec un contexte factice](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitations des doubles de test en mémoire EF  

Les doubles de test en mémoire peuvent être un bon moyen de fournir une couverture de niveau test unitaire des bits de votre application qui utilisent EF. Toutefois, lorsque vous procédez ainsi, vous utilisez LINQ to Objects pour exécuter des requêtes sur des données en mémoire. Cela peut entraîner un comportement différent de celui de l’utilisation du fournisseur LINQ d’EF (LINQ to Entities) pour convertir les requêtes en SQL exécuté sur votre base de données.  

Le chargement de données associées est un exemple de cette différence. Si vous créez une série de blogs qui ont chacun des publications associées, lorsque vous utilisez des données en mémoire, les publications associées sont toujours chargées pour chaque blog. Toutefois, lors de l’exécution sur une base de données, les données sont chargées uniquement si vous utilisez la méthode Include.  

Pour cette raison, il est recommandé de toujours inclure un certain niveau de test de bout en bout (en plus de vos tests unitaires) pour garantir le bon fonctionnement de votre application sur une base de données.  

## <a name="following-along-with-this-article"></a>Suivre les étapes de cet article  

Cet article fournit des listes de code complètes que vous pouvez copier dans Visual Studio pour suivre la procédure si vous le souhaitez. Il est plus facile de créer un **projet de test unitaire** et vous devrez cibler **.NET Framework 4,5** pour terminer les sections qui utilisent Async.  

## <a name="the-ef-model"></a>Le modèle EF  

Le service que nous allons tester utilise un modèle EF constitué du BloggingContext et du blog et des classes de publication. Ce code peut avoir été généré par le concepteur EF ou être un modèle de Code First.  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a>Propriétés DbSet virtuelles avec le concepteur EF  

Notez que les propriétés DbSet sur le contexte sont marquées comme virtuelles. Cela permettra à l’infrastructure factice de dériver de notre contexte et de remplacer ces propriétés par une implémentation fictive.  

Si vous utilisez Code First vous pouvez modifier vos classes directement. Si vous utilisez le concepteur EF, vous devez modifier le modèle T4 qui génère votre contexte. Ouvrez le \<model_name\> . Fichier Context.tt imbriqué sous votre fichier edmx, recherchez le fragment de code suivant et ajoutez le mot clé Virtual comme indiqué.  

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

## <a name="service-to-be-tested"></a>Service à tester  

Pour illustrer le test avec des doubles de test en mémoire, nous allons écrire deux tests pour un BlogService. Le service est en charge de la création de nouveaux blogs (AddBlog) et de la restauration de tous les blogs classés par nom (GetAllBlogs). En plus de GetAllBlogs, nous avons également fourni une méthode qui permet d’obtenir de façon asynchrone tous les blogs classés par nom (GetAllBlogsAsync).  

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

## <a name="testing-non-query-scenarios"></a>Test des scénarios non-requête  

C’est tout ce dont nous avons besoin pour commencer à tester des méthodes qui ne sont pas des requêtes. Le test suivant utilise MOQ pour créer un contexte. Il crée ensuite un DbSet \<Blog\> et le lie à la propriété blogs du contexte. Ensuite, le contexte est utilisé pour créer un nouveau BlogService qui est ensuite utilisé pour créer un nouveau blog, à l’aide de la méthode AddBlog. Enfin, le test vérifie que le service a ajouté un nouveau blog et a appelé SaveChanges dans le contexte.  

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

## <a name="testing-query-scenarios"></a>Test des scénarios de requête  

Pour pouvoir exécuter des requêtes sur notre double de test DbSet, nous devons configurer une implémentation de IQueryable. La première étape consiste à créer des données en mémoire : nous utilisons une liste \<Blog\> . Ensuite, nous créons un contexte et DBSet, puis nous mettons \<Blog\> en place l’implémentation IQueryable pour le DBSet : elles se délèguent simplement au fournisseur LINQ to Objects qui fonctionne avec la liste \<T\> .  

Nous pouvons ensuite créer un BlogService basé sur nos doubles de test et vous assurer que les données que nous obtenons de GetAllBlogs sont classées par nom.  

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

### <a name="testing-with-async-queries"></a>Test avec des requêtes Async

Entity Framework 6 a introduit un ensemble de méthodes d’extension qui peuvent être utilisées pour exécuter une requête de manière asynchrone. ToListAsync, FirstAsync, ForEachAsync, etc. sont des exemples de ces méthodes.  

Étant donné que les requêtes de Entity Framework utilisent LINQ, les méthodes d’extension sont définies sur IQueryable et IEnumerable. Toutefois, étant donné qu’elles sont uniquement conçues pour être utilisées avec Entity Framework vous pouvez recevoir l’erreur suivante si vous essayez de les utiliser sur une requête LINQ qui n’est pas une requête Entity Framework :

> L’IQueryable source n’implémente pas IDbAsyncEnumerable {0} . Seules les sources qui implémentent IDbAsyncEnumerable peuvent être utilisées pour Entity Framework opérations asynchrones. Pour plus d’informations [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) , consultez.  

Alors que les méthodes Async sont uniquement prises en charge lors d’une exécution sur une requête EF, vous pouvez les utiliser dans votre test unitaire en cas d’exécution sur un double de test en mémoire d’un DbSet.  

Pour pouvoir utiliser les méthodes Async, nous devons créer un DbAsyncQueryProvider en mémoire pour traiter la requête asynchrone. Bien qu’il soit possible de configurer un fournisseur de requêtes à l’aide de MOQ, il est beaucoup plus facile de créer une implémentation de double de test dans le code. Le code pour cette implémentation est le suivant :  

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

Maintenant que nous disposons d’un fournisseur de requêtes asynchrones, nous pouvons écrire un test unitaire pour notre nouvelle méthode GetAllBlogsAsync.  

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
