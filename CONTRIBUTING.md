# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="b9ed1-101">Contribution à la documentation Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b9ed1-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="b9ed1-102">Le processus d’ajout d’articles et d’exemples de code à la documentation d’Entity Framework est expliqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="b9ed1-103">Les contributions peuvent aller de la simple correction de fautes de frappe à la rédaction plus complexe de nouveaux articles.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="b9ed1-104">Comment apporter une correction ou suggestion simple</span><span class="sxs-lookup"><span data-stu-id="b9ed1-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="b9ed1-105">Les articles sont stockés dans le dépôt sous forme de fichiers Markdown.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="b9ed1-106">Pour changer simplement le contenu d’un fichier Markdown, cliquez sur le lien **Modifier** en haut à droite de la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="b9ed1-107">Vous devez peut-être développer la barre **Options** pour voir le lien **Modifier**.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="b9ed1-108">Suivez les instructions pour créer une demande de tirage (pull request).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="b9ed1-109">Après avoir examiné la demande de tirage, l’équipe EF l’accepte ou suggère des modifications.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="b9ed1-110">Comment effectuer une soumission plus complexe</span><span class="sxs-lookup"><span data-stu-id="b9ed1-110">How to make a more complex submission</span></span>

<span data-ttu-id="b9ed1-111">Vous devez avoir une connaissance de base de [Git et GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="b9ed1-112">Ouvrez un [dossier](https://github.com/dotnet/EntityFramework.Docs/issues/new) décrivant ce que vous voulez faire, par exemple modifier un article existant ou en créer un.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="b9ed1-113">Attendez l’approbation de l’équipe EF avant de vous investir davantage.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="b9ed1-114">EMBRANCHEZ le référentiel [dotnet/EntityFramework. docs](https://github.com/dotnet/EntityFramework.Docs/) et créez une branche pour vos modifications.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="b9ed1-115">Soumettez une demande de tirage dans la branche principale avec vos modifications.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-115">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="b9ed1-116">Répondez aux commentaires sur la demande de tirage.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="b9ed1-117">Syntaxe Markdown</span><span class="sxs-lookup"><span data-stu-id="b9ed1-117">Markdown syntax</span></span>

<span data-ttu-id="b9ed1-118">Les articles sont écrits en [DocFX Flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), un sur-ensemble de [GitHub Flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="b9ed1-119">Pour obtenir des exemples de syntaxe et métadonnées DFM illustrant les fonctionnalités d’interface utilisateur couramment utilisées dans la documentation EF, consultez [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) dans le guide de style du dépôt .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="b9ed1-120">Conventions relatives à la structure des dossiers</span><span class="sxs-lookup"><span data-stu-id="b9ed1-120">Folder structure conventions</span></span>

<span data-ttu-id="b9ed1-121">Les images et autres formes de contenu statique sont stockées dans un dossier `_static` dans chaque zone/dossier du site.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="b9ed1-122">Les exemples de code sont stockés dans le dossier racine `samples`.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="b9ed1-123">Ils sont organisés dans une structure de dossiers qui reproduit la structure de la documentation (figurant sous le dossier racine `entity-framework`).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="b9ed1-124">Extraits de code</span><span class="sxs-lookup"><span data-stu-id="b9ed1-124">Code snippets</span></span>

<span data-ttu-id="b9ed1-125">Les articles contiennent fréquemment des extraits de code pour illustrer les points abordés.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="b9ed1-126">DFM vous permet de copier du code dans le fichier Markdown ou de faire référence à un fichier de code distinct.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="b9ed1-127">Dans la mesure du possible, utilisez des fichiers de code distincts pour limiter les risques d’erreurs dans le code.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="b9ed1-128">Les fichiers de code doivent être stockés dans le dépôt, conformément à la structure de dossiers décrite ci-dessus pour les exemples de projets.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="b9ed1-129">Voici quelques exemples de [syntaxe d’extrait de code DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="b9ed1-130">Pour afficher un fichier de code entier en tant qu’extrait de code :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-130">To render an entire code file as a snippet:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="b9ed1-131">Pour afficher une partie d’un fichier en tant qu’extrait de code en utilisant des numéros de ligne :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-131">To render a portion of a file as a snippet by using line numbers:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="b9ed1-132">Pour les extraits de code C#, vous pouvez référencer une [région C#](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="b9ed1-133">Utiliser des régions plutôt que des numéros de ligne.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="b9ed1-134">Les numéros de ligne dans un fichier de code ont tendance à changer au point de ne plus être synchronisés avec les références de numéro de ligne dans un fichier Markdown.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="b9ed1-135">Les régions C# peuvent être imbriquées.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-135">C# regions can be nested.</span></span> <span data-ttu-id="b9ed1-136">Si vous référencez la région externe, les directives internes `#region` et `#endregion` ne sont pas affichées dans un extrait de code.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="b9ed1-137">Pour afficher une région C# nommée « snippet_Example » :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-137">To render a C# region named "snippet_Example":</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="b9ed1-138">Pour mettre en surbrillance les lignes sélectionnées dans un extrait de code affiché (généralement sous la forme d’un arrière-plan jaune) :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="b9ed1-139">Tester vos modifications avec DocFX</span><span class="sxs-lookup"><span data-stu-id="b9ed1-139">Test your changes with DocFX</span></span>

<span data-ttu-id="b9ed1-140">Testez vos modifications avec l’[outil en ligne de commande DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), qui crée une version hébergée localement du site.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="b9ed1-141">DocFX n’affiche pas le style et les extensions de site créés pour docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="b9ed1-142">DocFX nécessite le .NET Framework sur Windows ou Mono pour Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="b9ed1-143">Instructions pour Windows</span><span class="sxs-lookup"><span data-stu-id="b9ed1-143">Windows instructions</span></span>

* <span data-ttu-id="b9ed1-144">Téléchargez et décompressez *docfx.zip* à partir des [versions DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="b9ed1-145">Ajoutez DocFX à votre chemin (PATH).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="b9ed1-146">Dans une fenêtre de ligne de commande, accédez au dépôt cloné (qui contient le fichier *docfx.json*) et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ``` console
   docfx -t default --serve
   ```

* <span data-ttu-id="b9ed1-147">Dans un navigateur, accédez à `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="b9ed1-148">Instructions pour Mono</span><span class="sxs-lookup"><span data-stu-id="b9ed1-148">Mono instructions</span></span>

* <span data-ttu-id="b9ed1-149">Installez Mono via Homebrew : `brew install mono`.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="b9ed1-150">Téléchargez la [dernière version de DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span><span class="sxs-lookup"><span data-stu-id="b9ed1-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="b9ed1-151">Extrayez dans `\bin\docfx`.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="b9ed1-152">Créez un alias pour **docfx** :</span><span class="sxs-lookup"><span data-stu-id="b9ed1-152">Create an alias for **docfx**:</span></span>

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="b9ed1-153">Exécutez **docfx** dans le dépôt cloné pour générer le site, puis **docfx servi** pour afficher le site sur `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="b9ed1-154">Style et ton</span><span class="sxs-lookup"><span data-stu-id="b9ed1-154">Voice and tone</span></span>

<span data-ttu-id="b9ed1-155">Notre objectif est d’écrire une documentation facile à comprendre par le plus grand nombre.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="b9ed1-156">À cette fin, nous avons établi des recommandations pour le style rédactionnel que nous invitons nos contributeurs à suivre.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="b9ed1-157">Pour plus d’informations, consultez [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) dans le dépôt .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9ed1-157">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
