# <a name="contributing-to-the-entity-framework-documentation"></a>Contribution à la documentation Entity Framework

Le processus d’ajout d’articles et d’exemples de code à la documentation d’Entity Framework est expliqué ci-dessous. Les contributions peuvent aller de la simple correction de fautes de frappe à la rédaction plus complexe de nouveaux articles.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Comment apporter une correction ou suggestion simple

Les articles sont stockés dans le dépôt sous forme de fichiers Markdown. Pour changer simplement le contenu d’un fichier Markdown, cliquez sur le lien **Modifier** en haut à droite de la fenêtre du navigateur. Vous devez peut-être développer la barre **Options** pour voir le lien **Modifier**. Suivez les instructions pour créer une demande de tirage (pull request). Après avoir examiné la demande de tirage, l’équipe EF l’accepte ou suggère des modifications.

## <a name="how-to-make-a-more-complex-submission"></a>Comment effectuer une soumission plus complexe

Vous devez avoir une connaissance de base de [Git et GitHub.com](https://guides.github.com/activities/hello-world/).

* Ouvrez un [dossier](https://github.com/dotnet/EntityFramework.Docs/issues/new) décrivant ce que vous voulez faire, par exemple modifier un article existant ou en créer un. Attendez l’approbation de l’équipe EF avant de vous investir davantage.
* EMBRANCHEZ les référentiels [dotnet/EntityFramework.Doc](https://github.com/dotnet/EntityFramework.Docs/) et créez une branche pour vos modifications.
* Envoyez une demande de tirage (pull request) (PR) à main avec vos modifications.
* Répondez aux commentaires sur la demande de tirage.

## <a name="markdown-syntax"></a>Syntaxe Markdown

Les articles sont écrits en [DocFX Flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), un sur-ensemble de [GitHub Flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Pour obtenir des exemples de syntaxe et de métadonnées DFM pour les fonctionnalités de l’interface utilisateur communément utilisées dans la documentation d’EF, consultez [modèle de métadonnées et de démarques](https://docs.microsoft.com/contribute/dotnet/dotnet-style-guide).

## <a name="folder-structure-conventions"></a>Conventions relatives à la structure des dossiers

Les images et autres formes de contenu statique sont stockées dans un dossier `_static` dans chaque zone/dossier du site.

Les exemples de code sont stockés dans le dossier racine `samples`. Ils sont organisés dans une structure de dossiers qui reproduit la structure de la documentation (figurant sous le dossier racine `entity-framework`).

## <a name="code-snippets"></a>Extraits de code

Les articles contiennent fréquemment des extraits de code pour illustrer les points abordés. DFM vous permet de copier du code dans le fichier Markdown ou de faire référence à un fichier de code distinct. Dans la mesure du possible, utilisez des fichiers de code distincts pour limiter les risques d’erreurs dans le code. Les fichiers de code doivent être stockés dans le dépôt, conformément à la structure de dossiers décrite ci-dessus pour les exemples de projets.

Voici quelques exemples de [syntaxe d’extrait de code DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).

Pour afficher un fichier de code entier en tant qu’extrait de code :

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

Pour afficher une partie d’un fichier en tant qu’extrait de code en utilisant des numéros de ligne :

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

Pour les extraits de code C#, vous pouvez référencer une [région C#](https://msdn.microsoft.com/library/9a1ybwek.aspx). Utiliser des régions plutôt que des numéros de ligne. Les numéros de ligne dans un fichier de code ont tendance à changer au point de ne plus être synchronisés avec les références de numéro de ligne dans un fichier Markdown. Les régions C# peuvent être imbriquées. Si vous référencez la région externe, les directives internes `#region` et `#endregion` ne sont pas affichées dans un extrait de code.

Pour afficher une région C# nommée « snippet_Example » :

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

Pour mettre en surbrillance les lignes sélectionnées dans un extrait de code affiché (généralement sous la forme d’un arrière-plan jaune) :

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>Tester vos modifications avec DocFX

Testez vos modifications avec l’[outil en ligne de commande DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), qui crée une version hébergée localement du site. DocFX n’affiche pas le style et les extensions de site créés pour docs.microsoft.com.

DocFX nécessite le .NET Framework sur Windows ou Mono pour Linux ou macOS.

### <a name="windows-instructions"></a>Instructions pour Windows

* Téléchargez et décompressez *docfx.zip* à partir des [versions DocFX](https://github.com/dotnet/docfx/releases).
* Ajoutez DocFX à votre chemin (PATH).
* Dans une fenêtre de ligne de commande, accédez au dépôt cloné (qui contient le fichier *docfx.json*) et exécutez la commande suivante :

   ```console
   docfx -t default --serve
   ```

* Dans un navigateur, accédez à `http://localhost:8080`.

### <a name="mono-instructions"></a>Instructions pour Mono

* Installez Mono via Homebrew : `brew install mono`.
* Téléchargez la [dernière version de DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).
* Extrayez dans `\bin\docfx`.
* Créez un alias pour **docfx** :

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* Exécutez **docfx** dans le dépôt cloné pour générer le site, puis **docfx servi** pour afficher le site sur `http://localhost:8080`.

## <a name="voice-and-tone"></a>Voix et ton

Notre objectif est d’écrire une documentation facile à comprendre par le plus grand nombre. À cette fin, nous avons établi des recommandations pour le style rédactionnel que nous invitons nos contributeurs à suivre. Pour plus d’informations, consultez [recommandations relatives aux voix et aux tons](https://docs.microsoft.com/contribute/dotnet/dotnet-voice-tone).
