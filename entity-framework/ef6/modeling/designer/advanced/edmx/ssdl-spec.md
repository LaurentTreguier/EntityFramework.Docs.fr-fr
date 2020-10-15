---
title: Spécification SSDL-EF6
description: Spécification SSDL dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: 33052967bb543048fc0885957cf1f37abfac9cd2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066302"
---
# <a name="ssdl-specification"></a>Spécification SSDL
SSDL (Store Schema Definition Language) est un langage basé sur XML qui décrit le modèle de stockage d'une application Entity Framework.

Dans une application Entity Framework, les métadonnées du modèle de stockage sont chargées à partir d’un fichier. SSDL (écrit en SSDL) dans une instance de System. Data. Metadata. Edm. StoreItemCollection et sont accessibles à l’aide des méthodes de la classe System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework utilise les métadonnées du modèle de stockage pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques au stockage.

Le Entity Framework Designer (concepteur EF) stocke les informations de modèle de stockage dans un fichier. edmx au moment de la conception. Au moment de la génération, le Entity Designer utilise les informations d’un fichier. edmx pour créer le fichier. ssdl requis par Entity Framework au moment de l’exécution.

Les versions de SSDL sont différenciées par les espaces de noms XML.

| Version SSDL | Espace de noms XML                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association, élément (SSDL)

Un élément **Association** en Store Schema Definition Language (SSDL) spécifie des colonnes de table qui participent à une contrainte de clé étrangère dans la base de données sous-jacente. Deux éléments End enfants obligatoires spécifient des tables aux terminaisons de l'association et la multiplicité à chaque terminaison. Un élément ReferentialConstraint facultatif spécifie les terminaisons principales et dépendantes de l'association ainsi que les colonnes participantes. Si aucun élément **ReferentialConstraint** n’est présent, un élément AssociationSetMapping doit être utilisé pour spécifier les mappages de colonnes pour l’Association.

L’élément **Association** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   End (exactement deux éléments) ;
-   ReferentialConstraint (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Association** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom de la contrainte de clé étrangère correspondante dans la base de données sous-jacente. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK \_ ** de la clé étrangère :

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a>AssociationSet, élément (SSDL)

L’élément **AssociationSet** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère entre deux tables dans la base de données sous-jacente. Les colonnes de la table qui participent à la contrainte de clé étrangère sont spécifiées dans un élément Association. L’élément **Association** qui correspond à un élément **AssociationSet** donné est spécifié dans l’attribut **Association** de l’élément **AssociationSet** .

Les ensembles d'associations SSDL sont mappés aux ensembles d'associations CSDL par un élément AssociationSetMapping. Toutefois, si l’Association CSDL pour un ensemble d’associations CSDL donné est définie à l’aide d’un élément ReferentialConstraint, aucun élément **AssociationSetMapping** correspondant n’est nécessaire. Dans ce cas, si un élément **AssociationSetMapping** est présent, les mappages qu’il définit seront remplacés par l’élément **ReferentialConstraint** .

L’élément **AssociationSet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   End (zéro ou deux éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **AssociationSet** .

| Nom de l'attribut  | Est obligatoire | Valeur                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **Nom**        | Oui         | Nom de la contrainte de clé étrangère que l'ensemble d'associations représente.                          |
| **Association** | Oui         | Nom de l'association qui définit les colonnes qui participent à la contrainte de clé étrangère. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **AssociationSet** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **AssociationSet** qui représente la `FK_CustomerOrders` contrainte de clé étrangère dans la base de données sous-jacente :

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>Élément CollectionType (SSDL)

L’élément **CollectionType** en Store Schema Definition Language (SSDL) spécifie que le type de retour d’une fonction est une collection. L’élément **CollectionType** est un enfant de l’élément ReturnType. Le type de collection est spécifié à l’aide de l’élément enfant RowType :

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre une fonction qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes.

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a>CommandText, élément (SSDL)

L’élément **CommandText** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui vous permet de définir une instruction SQL qui est exécutée au niveau de la base de données. L’élément **CommandText** vous permet d’ajouter des fonctionnalités qui sont similaires à une procédure stockée dans la base de données, mais vous définissez l’élément **CommandText** dans le modèle de stockage.

L’élément **CommandText** ne peut pas avoir d’éléments enfants. Le corps de l’élément **CommandText** doit être une instruction SQL valide pour la base de données sous-jacente.

Aucun attribut n’est applicable à l’élément **CommandText** .

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Function** avec un élément **CommandText** enfant. Exposez la fonction **UpdateProductInOrder** en tant que méthode sur ObjectContext en l’important dans le modèle conceptuel.  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a>DefiningQuery, élément (SSDL)

L’élément **DefiningQuery** en Store Schema Definition Language (SSDL) vous permet d’exécuter une instruction SQL directement dans la base de données sous-jacente. L’élément **DefiningQuery** est couramment utilisé comme une vue de base de données, mais la vue est définie dans le modèle de stockage au lieu de la base de données. La vue définie dans un élément **DefiningQuery** peut être mappée à un type d’entité dans le modèle conceptuel via un élément EntitySetMapping. Ces mappages sont en lecture seule.  

La syntaxe SSDL suivante illustre la déclaration d’un **EntitySet** suivi de l’élément **DefiningQuery** qui contient une requête utilisée pour récupérer la vue.

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

Vous pouvez utiliser des procédures stockées dans le Entity Framework pour activer des scénarios de lecture-écriture sur des vues.Vous pouvez utiliser une vue de source de données ou une vue de Entity SQL comme table de base pour récupérer des données et pour le traitement des modifications par des procédures stockées.

Vous pouvez utiliser l’élément **DefiningQuery** pour cibler Microsoft SQL Server Compact 3,5. Bien que SQL Server Compact 3,5 ne prenne pas en charge les procédures stockées, vous pouvez implémenter des fonctionnalités similaires avec l’élément **DefiningQuery** . Cet élément peut s'avérer également utile pour créer des procédures stockées afin de surmonter une incompatibilité entre les types de données utilisés dans le langage de programmation et ceux de la source de données. Vous pouvez écrire un **DefiningQuery** qui accepte un certain ensemble de paramètres, puis appelle une procédure stockée avec un jeu de paramètres différent, par exemple, une procédure stockée qui supprime des données.

## <a name="dependent-element-ssdl"></a>Élément Dependent (SSDL)

L’élément **dépendant** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison dépendante d’une contrainte de clé étrangère (également appelée contrainte référentielle). L’élément **dépendant** spécifie la ou les colonnes d’une table qui font référence à une ou plusieurs colonnes de clé primaire. Les éléments **PropertyRef** spécifient les colonnes qui sont référencées. L’élément principal spécifie les colonnes de clés primaires référencées par les colonnes spécifiées dans l’élément **dépendant** .

L’élément **dépendant** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   PropertyRef (un ou plusieurs) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **dépendant** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Rôle**       | Oui         | La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; dans le cas contraire, il s’agit du nom de la table qui contient la colonne de référence. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **dépendant** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK \_ ** . L’élément **dépendant** spécifie la colonne **CustomerID** de la table **Order** comme terminaison dépendante de la contrainte.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a>Documentation, élément (SSDL)

L’élément **documentation** en Store Schema Definition Language (SSDL) peut être utilisé pour fournir des informations sur un objet défini dans un élément parent.

L’élément **documentation** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   **Summary**: brève description de l’élément parent. (zéro ou un élément).
-   **LongDescription**: description complète de l’élément parent. (zéro ou un élément).

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **documentation** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément de **documentation** en tant qu’élément enfant d’un élément EntityType.

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a>End, élément (SSDL)

L’élément **end** en Store Schema Definition Language (SSDL) spécifie la table et le nombre de lignes à une terminaison d’une contrainte FOREIGN KEY dans la base de données sous-jacente. L’élément **end** peut être un enfant de l’élément Association ou de l’élément AssociationSet. Dans chaque cas, les éléments enfants et les attributs applicables possibles sont différents.

### <a name="end-element-as-a-child-of-the-association-element"></a>Élément End comme enfant de l'élément Association

Un élément **end** (en tant qu’enfant de l’élément **Association** ) spécifie la table et le nombre de lignes à la fin d’une contrainte de clé étrangère, respectivement avec les attributs **type** et **Multiplicity** . Les terminaisons d'une contrainte de clé étrangère sont définies dans le cadre d'un ensemble d'associations SSDL ; un ensemble d'associations SSDL doit avoir exactement deux terminaisons.

Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   OnDelete (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **final** lorsqu’il est l’enfant d’un élément **Association** .

| Nom de l'attribut   | Est obligatoire | Valeur                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | Oui         | Nom complet du jeu d'entités SSDL qui est à la terminaison de la contrainte de clé étrangère.                                                                                                                                                                                                                                                                                          |
| **Rôle**         | Non          | Valeur de l’attribut **role** dans l’élément principal ou dépendant de l’élément ReferentialConstraint correspondant (s’il est utilisé).                                                                                                                                                                                                                                             |
| **Multiplicité** | Oui         | **1**, **0.. 1**, ou **\*** selon le nombre de lignes qui peuvent être à la fin de la contrainte de clé étrangère. <br/> **1** indique qu’une seule ligne existe à la fin de la contrainte de clé étrangère. <br/> **0.. 1** indique qu’il existe zéro ou une ligne à la fin de la contrainte de clé étrangère. <br/> **\*** indique que zéro, une ou plusieurs lignes existent à la fin de la contrainte de clé étrangère. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère **FK \_ ** . Les valeurs de **multiplicité** spécifiées sur chaque élément de **fin** indiquent que de nombreuses lignes de la table **Orders** peuvent être associées à une ligne de la table **Customers** , mais qu’une seule ligne de la table **Customers** peut être associée à une ligne dans la table **Orders** . En outre, l’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Élément End comme enfant de l'élément AssociationSet

L’élément **end** (en tant qu’enfant de l’élément **AssociationSet** ) spécifie une table à une terminaison d’une contrainte de clé étrangère dans la base de données sous-jacente.

Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **end** lorsqu’il est l’enfant d’un élément **AssociationSet** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet ;**  | Oui         | Nom du jeu d'entités SSDL qui est à la terminaison de la contrainte de clé étrangère.                                      |
| **Rôle**       | Non          | Valeur de l’un des attributs de **rôle** spécifiés sur un élément de **fin** de l’élément Association correspondant. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** avec un élément **AssociationSet** avec deux éléments **end** :

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a>EntityContainer, élément (SSDL)

Un élément **EntityContainer** en Store Schema Definition Language (SSDL) décrit la structure de la source de données sous-jacente dans une application Entity Framework : les jeux d’entités SSDL (définis dans les éléments EntitySet) représentent les tables d’une base de données, les types d’entités SSDL (définis dans les éléments EntityType) représentent les lignes d’une table, et les ensembles d’associations (définis dans les éléments AssociationSet) représentent des contraintes Un conteneur d'entités de modèle de stockage est mappé à un conteneur d'entités de modèle conceptuel via l'élément EntityContainerMapping.

Un élément **EntityContainer** peut avoir zéro ou un élément documentation. Si un élément de **documentation** est présent, il doit précéder tous les autres éléments enfants.

Un élément **EntityContainer** peut avoir zéro ou plusieurs des éléments enfants suivants (dans l’ordre indiqué) :

-   EntitySet ;
-   AssociationSet ;
-   éléments d'annotation.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityContainer** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom du conteneur d'entités. Ce nom ne peut pas contenir de point (.). |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityContainer** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** qui définit deux jeux d’entités et un ensemble d’associations. Notez que les noms de type d'entité et de type d'association sont qualifiés par le nom de l'espace de noms du modèle conceptuel.

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a>EntitySet, élément (SSDL)

Un élément **EntitySet** en Store Schema Definition Language (SSDL) représente une table ou une vue dans la base de données sous-jacente. Un élément EntityType en SSDL représente une ligne dans la table ou la vue. L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL. Le mappage entre un jeu d'entités CSDL et un jeu d'entités SSDL est spécifié dans un élément EntitySetMapping.

L’élément **EntitySet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   DefiningQuery (zéro ou un élément) ;
-   éléments d'annotation.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntitySet** .

> [!NOTE]
> Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** . Ces attributs sont utilisés par l'Assistant Mise à jour du modèle lors de la mise à jour d'un modèle.

| Nom de l'attribut | Est obligatoire | Valeur                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom du jeu d'entités.                                                              |
| **EntityType** | Oui         | Nom qualifié complet du type d'entité pour lequel le jeu d'entités contient des instances. |
| **Schéma**     | Non          | Schéma de base de données.                                                                     |
| **Table**      | Non          | Table de base de données.                                                                      |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntitySet** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** qui a deux éléments **EntitySet** et un élément **AssociationSet** :

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a>EntityType, élément (SSDL)

Un élément **EntityType** en Store Schema Definition Language (SSDL) représente une ligne dans une table ou une vue de la base de données sous-jacente. Un élément EntitySet en SSDL représente la table ou la vue dans laquelle les lignes résultent. L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL. Le mappage entre un type d'entité SSDL et un type d'entité CSDL est spécifié dans un élément EntityTypeMapping.

L’élément **EntityType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Key (zéro ou un élément) ;
-   éléments d'annotation.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityType** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Le nom du type d’entité. Cette valeur est habituellement la même que le nom de la table dans laquelle le type d'entité représente une ligne. Cette valeur ne peut pas contenir de point (.). |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec deux propriétés :

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a>Function, élément (SSDL)

L’élément **Function** de Store Schema Definition Language (SSDL) spécifie une procédure stockée qui existe dans la base de données sous-jacente.

L’élément **Function** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Paramètre (zéro, un ou plusieurs)
-   CommandText (zéro ou un)
-   ReturnType (zéro, un ou plusieurs)
-   éléments d'annotation (zéro, un ou plusieurs).

Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** ou l’attribut **ReturnType** (voir ci-dessous), mais pas les deux.

Les procédures stockées spécifiées dans le modèle de stockage peuvent être importées dans le modèle conceptuel d'une application. Pour plus d’informations, consultez [interrogation avec des procédures stockées](xref:ef6/modeling/designer/stored-procedures/query). L’élément **Function** peut également être utilisé pour définir des fonctions personnalisées dans le modèle de stockage.  

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Function** .

> [!NOTE]
> Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** . Ces attributs sont utilisés par l'Assistant Mise à jour du modèle lors de la mise à jour d'un modèle.

| Nom de l'attribut             | Est obligatoire | Valeur                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**                   | Oui         | Nom de la procédure stockée.                                                                                                                                                                                  |
| **ReturnType**             | Non          | Type de retour de la procédure stockée.                                                                                                                                                                           |
| **Agrégat**              | Non          | **True** si la procédure stockée retourne une valeur d’agrégation ; Sinon, **false**.                                                                                                                                  |
| **Intégrée**                | Non          | **True** si la fonction est une fonction intégrée<sup>1</sup> ; Sinon, **false**.                                                                                                                                  |
| **StoreFunctionName**      | Non          | Nom de la procédure stockée.                                                                                                                                                                                  |
| **NiladicFunction**        | Non          | **True** si la fonction est une fonction niladic<sup>2</sup> ; **False** dans le cas contraire.                                                                                                                                   |
| **IsComposable**           | Non          | **True** si la fonction est une fonction composable<sup>3</sup> ; **False** dans le cas contraire.                                                                                                                                |
| **ParameterTypeSemantics** | Non          | Énumération qui définit la sémantique de type utilisée pour résoudre les surcharges de fonction. L'énumération est définie dans le manifeste du fournisseur par définition de fonction. La valeur par défaut est **AllowImplicitConversion**. |
| **Schéma**                 | Non          | Nom du schéma dans lequel une procédure stockée est définie.                                                                                                                                                   |

<sup>1</sup> une fonction intégrée est une fonction définie dans la base de données. Pour plus d’informations sur les fonctions définies dans le modèle de stockage, consultez CommandText, élément (SSDL).

<sup>2</sup> une fonction niladic est une fonction qui n’accepte aucun paramètre et, lorsqu’elle est appelée, elle ne requiert pas de parenthèses.

<sup>3</sup> deux fonctions sont composables si la sortie d’une fonction peut être l’entrée de l’autre fonction.

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fonction** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Function** qui correspond à la procédure stockée **UpdateOrderQuantity** . La procédure stockée accepte deux paramètres et ne retourne pas de valeur.

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a>Key, élément (SSDL)

L’élément **clé** en Store Schema Definition Language (SSDL) représente la clé primaire d’une table dans la base de données sous-jacente. **Key** est un élément enfant d’un élément EntityType, qui représente une ligne dans une table. La clé primaire est définie dans l’élément **Key** en référençant un ou plusieurs éléments Property définis sur l’élément **EntityType** .

L’élément **Key** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   PropertyRef (un ou plusieurs) ;
-   éléments d'annotation.

Aucun attribut n’est applicable à l’élément **Key** .

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec une clé qui référence une propriété :

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a>OnDelete, élément (SSDL)

L’élément **OnDelete** en Store Schema Definition Language (SSDL) reflète le comportement de la base de données lorsqu’une ligne qui participe à une contrainte de clé étrangère est supprimée. Si l’action est définie sur **cascade**, les lignes qui font référence à une ligne en cours de suppression seront également supprimées. Si l’action est définie sur **aucun**, les lignes qui font référence à une ligne en cours de suppression ne sont pas supprimées. Un élément **OnDelete** est un élément enfant d’un élément end.

Un élément **OnDelete** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **OnDelete** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **Action**     | Oui         | **Cascade** ou **None**. (La valeur **Restricted** est valide mais a le même comportement qu' **aucun**.) |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **OnDelete** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère **FK \_ ** . L’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a>Élément Parameter (SSDL)

L’élément **Parameter** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui spécifie les paramètres d’une procédure stockée dans la base de données.

L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Le nom du paramètre.                                                                                                                                                                                                      |
| **Type**       | Oui         | Le type du paramètre.                                                                                                                                                                                                             |
| **Mode**       | Non          | **In**, **out**ou **INOUT** selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie.                                                                                                                |
| **MaxLength**  | Non          | Longueur maximale du paramètre.                                                                                                                                                                                            |
| **Précision**  | Non          | La précision du paramètre.                                                                                                                                                                                                 |
| **Mettre à l'échelle**      | Non          | L’échelle du paramètre.                                                                                                                                                                                                     |
| **SRID**       | Non          | Identificateur de référence système spatial. Valide uniquement pour les paramètres des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Function** qui possède deux éléments **Parameter** qui spécifient des paramètres d’entrée :

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a>Élément Principal (SSDL)

L’élément **principal** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison principale d’une contrainte de clé étrangère (également appelée contrainte référentielle). L’élément **principal** spécifie la ou les colonnes de clé primaire d’une table qui est référencée par une ou plusieurs colonnes. Les éléments **PropertyRef** spécifient les colonnes qui sont référencées. L’élément dépendant spécifie les colonnes qui référencent les colonnes de clés primaires spécifiées dans l’élément **principal** .

L’élément **principal** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   PropertyRef (un ou plusieurs) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **principal** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Rôle**       | Oui         | La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; Sinon, le nom de la table qui contient la colonne référencée. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **principal** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK \_ ** . L’élément **principal** spécifie la colonne **CustomerID** de la table **Customer** comme terminaison principale de la contrainte.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a>Property, élément (SSDL)

L’élément de **propriété** en Store Schema Definition Language (SSDL) représente une colonne dans une table de la base de données sous-jacente. Les éléments de **propriété** sont des enfants d’éléments EntityType, qui représentent des lignes dans une table. Chaque élément de **propriété** défini sur un élément **EntityType** représente une colonne.

Un élément de **propriété** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**                  | Oui         | Nom de la colonne correspondante.                                                                                                                                                                                           |
| **Type**                  | Oui         | Type de la colonne correspondante.                                                                                                                                                                                           |
| **Nullable**              | Non          | **True** (valeur par défaut) ou **false** selon que la colonne correspondante peut avoir une valeur null ou non.                                                                                                                  |
| **DefaultValue**          | Non          | Valeur par défaut de la colonne correspondante.                                                                                                                                                                                  |
| **MaxLength**             | Non          | Longueur maximale de la colonne correspondante.                                                                                                                                                                                 |
| **Multiple**           | Non          | **True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne de longueur fixe.                                                                                                              |
| **Précision**             | Non          | Précision de la colonne correspondante.                                                                                                                                                                                      |
| **Mettre à l'échelle**                 | Non          | Échelle de la colonne correspondante.                                                                                                                                                                                          |
| **Unicode**               | Non          | **True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne Unicode.                                                                                                                   |
| **Classement**             | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                   |
| **SRID**                  | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **StoreGeneratedPattern** | Non          | **None**, **Identity** (si la valeur de colonne correspondante est une identité générée dans la base de données) ou **calculée** (si la valeur de colonne correspondante est calculée dans la base de données). Non valide pour les propriétés RowType. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec deux éléments de **propriété** enfants :

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a>Élément PropertyRef (SSDL)

L’élément **PropertyRef** en Store Schema Definition Language (SSDL) fait référence à une propriété définie sur un élément EntityType pour indiquer que la propriété effectuera l’un des rôles suivants :

-   Faire partie de la clé primaire de la table représentée par l' **EntityType** . Un ou plusieurs éléments **PropertyRef** peuvent être utilisés pour définir une clé primaire. Pour plus d'informations, consultez Élément Key.
-   Faire partie de la terminaison dépendante ou principale d'une contrainte référentielle. Pour plus d'informations, consultez Élément ReferentialConstraint.

L’élément **PropertyRef** ne peut avoir que les éléments enfants suivants :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **PropertyRef** .

| Nom de l'attribut | Est obligatoire | Valeur                                |
|:---------------|:------------|:-------------------------------------|
| **Nom**       | Oui         | Nom de la propriété référencée. |

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **PropertyRef** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **PropertyRef** utilisé pour définir une clé primaire en référençant une propriété définie sur un élément **EntityType** .

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a>ReferentialConstraint, élément (SSDL)

L’élément **ReferentialConstraint** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère (également appelée contrainte d’intégrité référentielle) dans la base de données sous-jacente. Les terminaisons principale et dépendante de la contrainte sont spécifiées respectivement par les éléments enfants Principal et Dependent. Les colonnes qui participent aux terminaisons principale et dépendante sont référencées avec les éléments PropertyRef.

L’élément **ReferentialConstraint** est un élément enfant facultatif de l’élément Association. Si un élément **ReferentialConstraint** n’est pas utilisé pour mapper la contrainte de clé étrangère spécifiée dans l’élément **Association** , un élément AssociationSetMapping doit être utilisé pour ce faire.

L’élément **ReferentialConstraint** peut avoir les éléments enfants suivants :

-   Documentation (zéro ou un élément) ;
-   Principal (exactement un élément) ;
-   Dependent (exactement un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReferentialConstraint** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK \_ ** de la clé étrangère :

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a>ReturnType, élément (SSDL)

L’élément **ReturnType** en Store Schema Definition Language (SSDL) spécifie le type de retour pour une fonction définie dans un élément **Function** . Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .

Le type de retour d’une fonction est spécifié à l’aide de l’attribut **type** ou de l’élément **ReturnType** .

L’élément **ReturnType** peut avoir les éléments enfants suivants :

- CollectionType (un)  

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant utilise une **fonction** qui retourne une collection de lignes.

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a>RowType, élément (SSDL)

Un élément **RowType** en Store Schema Definition Language (SSDL) définit une structure sans nom comme type de retour pour une fonction définie dans le magasin.

Un élément **RowType** est l’élément enfant de l’élément **CollectionType** :

Un élément **RowType** peut avoir les éléments enfants suivants :

- Property (un ou plusieurs) ;  

### <a name="example"></a>Exemple

L’exemple suivant montre une fonction de magasin qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a>Schema, élément (SSDL)

L’élément **Schema** en Store Schema Definition Language (SSDL) est l’élément racine d’une définition de modèle de stockage. Il contient des définitions pour les objets, les fonctions et les conteneurs qui composent un modèle de stockage.

L’élément **Schema** peut contenir zéro, un ou plusieurs des éléments enfants suivants :

-   Association
-   EntityType
-   EntityContainer
-   Fonction

L’élément **Schema** utilise l’attribut **namespace** pour définir l’espace de noms du type d’entité et des objets Association dans un modèle de stockage. Dans un espace de noms, deux objets ne peuvent pas avoir le même nom.

Un espace de noms de modèle de stockage est différent de l’espace de noms XML de l’élément de **schéma** . Un espace de noms de modèle de stockage (tel que défini par l’attribut d' **espace de noms** ) est un conteneur logique pour les types d’entités et les types d’association. L’espace de noms XML (indiqué par l’attribut **xmlns** ) d’un élément de **schéma** est l’espace de noms par défaut pour les éléments enfants et les attributs de l’élément de **schéma** . Les espaces de noms XML de la forme https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (où aaaa et mm représentent respectivement une année et un mois) sont réservés au langage SSDL. Des éléments et attributs personnalisés ne peuvent pas être dans des espaces de noms de cette forme.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Schema** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espace de noms**             | Oui         | Espace de noms du modèle de stockage. La valeur de l’attribut d' **espace de noms** est utilisée pour former le nom qualifié complet d’un type. Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store, le nom qualifié complet de l' **EntityType** est ExampleModel. Store. Customer. <br/> Les chaînes suivantes ne peuvent pas être utilisées comme valeur pour l’attribut d' **espace de noms** : **System**, **transient**ou **EDM**. La valeur de l’attribut d' **espace de noms** ne peut pas être la même que la valeur de l’attribut d' **espace de noms** dans l’élément de schéma CSDL. |
| **Alias**                 | Non          | Identificateur utilisé à la place du nom de l'espace de noms. Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store et que la valeur de l’attribut **alias** est *StorageModel*, vous pouvez utiliser StorageModel. Customer comme nom qualifié complet de l' **EntityType.**                                                                                                                                                                                                                                                                                    |
| **Fournisseur**              | Oui         | Fournisseur de données.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | Oui         | Jeton qui indique au fournisseur quel manifeste de fournisseur retourner. Aucun format n'est défini pour le jeton. Les valeurs du jeton sont définies par le fournisseur. Pour plus d’informations sur les jetons de manifeste du fournisseur SQL Server, consultez SqlClient pour Entity Framework.                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>Exemple

L’exemple suivant illustre un élément de **schéma** qui contient un élément **EntityContainer** , deux éléments **EntityType** et un élément **Association** .

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a>Attributs d'annotation

Les attributs d'annotation en SSDL (Store Schema Definition Language) sont des attributs XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant les éléments dans le modèle de stockage. En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux attributs d'annotation :

-   Les attributs d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage SSDL.
-   Les noms qualifiés complets de deux attributs d'annotation ne doivent pas être identiques.

Plusieurs attributs d'annotation peuvent être appliqués à un élément SSDL donné. Vous pouvez accéder aux métadonnées contenues dans les éléments d’annotation au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément EntityType qui a un attribut d’annotation appliqué à la propriété **OrderID** . L’exemple montre également un élément d’annotation ajouté à l’élément **EntityType** .

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a>Éléments d'annotation (SSDL)

Les éléments d'annotation en SSDL (Store Schema Definition Language) sont des éléments XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant le modèle de stockage. En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux éléments d'annotation :

-   Les éléments d'annotation ne doivent pas figurer dans un espace de noms XML réservé au langage SSDL.
-   Les noms qualifiés complets de deux éléments d'annotation ne doivent pas être identiques.
-   Les éléments d'annotation doivent apparaître après tous les autres éléments enfants d'un élément SSDL donné.

Plusieurs éléments d'annotation peuvent être des enfants d'un élément SSDL donné. À partir de la .NET Framework version 4, les métadonnées contenues dans les éléments d’annotation sont accessibles au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément EntityType qui a un élément annotation (**customelement**). L’exemple montre également un attribut d’annotation appliqué à la propriété **OrderID** .

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a>Facettes (SSDL)

Les facettes en SSDL (Store Schema Definition Language) représentent des contraintes sur les types de colonne spécifiés dans les éléments Property. Les facettes sont implémentées en tant qu’attributs XML sur les éléments de **propriété** .

Le tableau ci-dessous décrit les facettes prises en charge dans le langage SSDL :

| Facette           | Description                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Classement**   | Spécifie la table de classement ou ordre de tri à utiliser lors de l'exécution d'opérations de comparaison et de tri sur des valeurs de la propriété.                                                                                                             |
| **Multiple** | Spécifie si la longueur de la valeur de colonne peut varier.                                                                                                                                                                                                  |
| **MaxLength**   | Spécifie la longueur maximale de la valeur de colonne.                                                                                                                                                                                                           |
| **Précision**   | Pour les propriétés de type **Decimal**, spécifie le nombre de chiffres qu’une valeur de propriété peut avoir. Pour les propriétés de type **Time**, **DateTime**et **DateTimeOffset**, spécifie le nombre de chiffres pour la partie fractionnaire des secondes de la valeur de colonne. |
| **Mettre à l'échelle**       | Spécifie le nombre de chiffres à droite de la virgule décimale pour la valeur de colonne.                                                                                                                                                                      |
| **Unicode**     | Indique si la valeur de colonne est stockée au format Unicode.                                                                                                                                                                                                    |
