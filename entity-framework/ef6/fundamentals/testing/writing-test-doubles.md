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
# <a name="testing-with-your-own-test-doubles"></a>Test avec vos propres doubles de test
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Lors de l’écriture de tests pour votre application, il est souvent préférable d’éviter d’atteindre la base de données.  Entity Framework vous permet d’y parvenir en créant un contexte, avec un comportement défini par vos tests, qui utilise des données en mémoire.  

## <a name="options-for-creating-test-doubles"></a>Options de création de doubles de test  

Il existe deux approches différentes qui peuvent être utilisées pour créer une version en mémoire de votre contexte.  

- **Créer vos propres doubles de test** : cette approche implique l’écriture de votre propre implémentation en mémoire de votre contexte et DbSets. Cela vous donne un grand contrôle sur la façon dont les classes se comportent, mais peut impliquer l’écriture et la possession d’une quantité raisonnable de code.  
- **Utilisez une infrastructure fictive pour créer des doubles de test** : à l’aide d’une infrastructure fictive (telle que MOQ), vous pouvez avoir les implémentations en mémoire de votre contexte et les jeux créés dynamiquement au moment de l’exécution.  

Cet article traite de la création de votre propre double de test. Pour plus d’informations sur l’utilisation d’une infrastructure fictive, consultez [test avec un Framework fictif](xref:ef6/fundamentals/testing/mocking).  

## <a name="testing-with-pre-ef6-versions"></a>Test avec les versions antérieures à EF6  

Le code présenté dans cet article est compatible avec EF6. Pour tester avec EF5 et les versions antérieures, consultez [test avec un contexte factice](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitations des doubles de test en mémoire EF  

Les doubles de test en mémoire peuvent être un bon moyen de fournir une couverture de niveau test unitaire des bits de votre application qui utilisent EF. Toutefois, lorsque vous procédez ainsi, vous utilisez LINQ to Objects pour exécuter des requêtes sur des données en mémoire. Cela peut entraîner un comportement différent de celui de l’utilisation du fournisseur LINQ d’EF (LINQ to Entities) pour convertir les requêtes en SQL exécuté sur votre base de données.  

Le chargement de données associées est un exemple de cette différence. Si vous créez une série de blogs qui ont chacun des publications associées, lorsque vous utilisez des données en mémoire, les publications associées sont toujours chargées pour chaque blog. Toutefois, lors de l’exécution sur une base de données, les données sont chargées uniquement si vous utilisez la méthode Include.  

Pour cette raison, il est recommandé de toujours inclure un certain niveau de test de bout en bout (en plus de vos tests unitaires) pour garantir le bon fonctionnement de votre application sur une base de données.  

## <a name="following-along-with-this-article"></a>Suivre les étapes de cet article  

Cet article fournit des listes de code complètes que vous pouvez copier dans Visual Studio pour suivre la procédure si vous le souhaitez. Il est plus facile de créer un **projet de test unitaire** et vous devrez cibler **.NET Framework 4,5** pour terminer les sections qui utilisent Async.  

## <a name="creating-a-context-interface"></a>Création d’une interface de contexte  

Nous allons examiner le test d’un service qui utilise un modèle EF. Pour pouvoir remplacer notre contexte EF par une version en mémoire à des fins de test, nous allons définir une interface qui sera implémentée par notre contexte EF (et c’est en mémoire double).

Le service que nous allons tester interroge et modifie les données à l’aide des propriétés DbSet de notre contexte et appelle également SaveChanges pour envoyer les modifications à la base de données. Nous incluons donc ces membres sur l’interface.  

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

## <a name="the-ef-model"></a>Le modèle EF  

Le service que nous allons tester utilise un modèle EF constitué du BloggingContext et du blog et des classes de publication. Ce code peut avoir été généré par le concepteur EF ou être un modèle de Code First.  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>Implémentation de l’interface de contexte à l’aide du concepteur EF  

Notez que notre contexte implémente l’interface IBloggingContext.  

Si vous utilisez Code First vous pouvez modifier votre contexte directement pour implémenter l’interface. Si vous utilisez le concepteur EF, vous devez modifier le modèle T4 qui génère votre contexte. Ouvrez le \<model_name\> . Fichier Context.tt imbriqué sous votre fichier edmx, recherchez le fragment de code suivant et ajoutez-le dans l’interface, comme indiqué.  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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

## <a name="creating-the-in-memory-test-doubles"></a>Création des doubles de test en mémoire  

Maintenant que nous disposons du véritable modèle EF et du service qui peut l’utiliser, il est temps de créer le double de test en mémoire que nous pouvons utiliser pour le test. Nous avons créé un double de test TestContext pour notre contexte. Dans les doubles de test, nous avons choisi le comportement que nous souhaitons pour prendre en charge les tests que nous allons exécuter. Dans cet exemple, nous capturons simplement le nombre de fois que SaveChanges est appelé, mais vous pouvez inclure la logique nécessaire pour vérifier le scénario que vous testez.  

Nous avons également créé un TestDbSet qui fournit une implémentation en mémoire de DbSet. Nous avons fourni une implémentation complète pour toutes les méthodes sur DbSet (à l’exception de Find), mais vous devez uniquement implémenter les membres que votre scénario de test utilisera.  

TestDbSet utilise d’autres classes d’infrastructure que nous avons incluses pour s’assurer que les requêtes asynchrones peuvent être traitées.  

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

### <a name="implementing-find"></a>Implémentation de Find  

La méthode Find est difficile à implémenter de manière générique. Si vous devez tester du code qui utilise la méthode Find, il est plus facile de créer un DbSet de test pour chacun des types d’entité devant prendre en charge Find. Vous pouvez ensuite écrire une logique pour rechercher ce type particulier d’entité, comme indiqué ci-dessous.  

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

## <a name="writing-some-tests"></a>Écrire des tests  

C’est tout ce dont nous avons besoin pour commencer le test. Le test suivant crée un TestContext, puis un service basé sur ce contexte. Le service est ensuite utilisé pour créer un blog, à l’aide de la méthode AddBlog. Enfin, le test vérifie que le service a ajouté un nouveau blog à la propriété blogs du contexte et a appelé SaveChanges sur le contexte.  

Il s’agit simplement d’un exemple des types de choses que vous pouvez tester avec un double de test en mémoire et vous pouvez ajuster la logique des doubles de test et la vérification pour répondre à vos besoins.  

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

Voici un autre exemple de test : cette fois-ci, il exécute une requête. Le test commence par la création d’un contexte de test avec des données dans sa propriété de blog. Notez que les données ne sont pas classées par ordre alphabétique. Nous pouvons ensuite créer un BlogService basé sur notre contexte de test et vous assurer que les données renvoyées par GetAllBlogs sont classées par nom.  

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

Enfin, nous allons écrire un test supplémentaire qui utilise notre méthode Async pour s’assurer que l’infrastructure Async que nous avons incluse dans [TestDbSet](#creating-the-in-memory-test-doubles) fonctionne.  

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
