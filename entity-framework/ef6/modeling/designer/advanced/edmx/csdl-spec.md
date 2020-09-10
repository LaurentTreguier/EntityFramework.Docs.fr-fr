---
title: Spécification CSDL-EF6
description: Spécification CSDL dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 0137eba39bd719b8987dad2eb16645475a00d94c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620626"
---
# <a name="csdl-specification"></a>Spécification CSDL
Le langage CSDL (Conceptual Schema Definition Language) est un langage basé sur XML qui décrit les entités, relations et fonctions qui composent un modèle conceptuel d'une application pilotée par les données. Ce modèle conceptuel peut être utilisé par le Entity Framework ou WCF Data Services. Les métadonnées qui sont décrites avec le langage CSDL sont utilisées par le Entity Framework pour mapper les entités et les relations définies dans un modèle conceptuel à une source de données. Pour plus d’informations, consultez [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) et [spécification MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).

Le langage CSDL est l’implémentation de la Entity Framework du Entity Data Model.

Dans une application Entity Framework, les métadonnées du modèle conceptuel sont chargées à partir d’un fichier. CSDL (écrit en CSDL) dans une instance de System. Data. Metadata. Edm. EdmItemCollection et sont accessibles à l’aide des méthodes de la classe System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework utilise les métadonnées du modèle conceptuel pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques à la source de données.

Le concepteur EF stocke les informations de modèle conceptuel dans un fichier. edmx au moment de la conception. Au moment de la génération, le concepteur EF utilise les informations d’un fichier. edmx pour créer le fichier. csdl requis par Entity Framework au moment de l’exécution.

Les versions de CSDL sont différenciées par les espaces de noms XML.

| Version CSDL | Espace de noms XML                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association, élément (CSDL)

Un élément **Association** définit une relation entre deux types d’entités. Une association doit spécifier les types d'entités impliqués dans la relation et le nombre possible de types d'entités à chaque terminaison de la relation, appelé « multiplicité ». La multiplicité d’une terminaison d’association peut avoir la valeur un (1), zéro ou un (0.. 1), ou plusieurs ( \* ). Ces informations sont spécifiées dans deux éléments End enfants.

Il est possible d'accéder aux instances de type d'entité au niveau d'une terminaison d'une association via les propriétés de navigation ou les clés étrangères, si elles sont exposées sur un type d'entité.

Dans une application, une instance d'une association représente une association spécifique entre des instances de types d'entités. Les instances d'association sont regroupées logiquement dans un ensemble d'associations.

Un élément **Association** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   End (exactement 2 éléments) ;
-   ReferentialConstraint (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Association** .

| Nom de l'attribut | Est obligatoire | Valeur                        |
|:---------------|:------------|:-----------------------------|
| **Nom**       | Oui         | Nom de l'association. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** lorsque les clés étrangères n’ont pas été exposées sur les types d’entités **Customer** et **Order** . Les valeurs de **multiplicité** pour chaque **terminaison** de l’Association indiquent que de nombreuses **commandes** peuvent être associées à un **client**, mais qu’un seul **client** peut être associé à une **commande**. En outre, l’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext seront supprimées si le **client** est supprimé.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** lorsque des clés étrangères ont été exposées sur les types d’entités **Customer** et **Order** . Avec les clés étrangères exposées, la relation entre les entités est gérée à l’aide d’un élément **ReferentialConstraint** . Un élément AssociationSetMapping correspondant n'est pas nécessaire pour mapper cette association à la source de données.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a>AssociationSet, élément (CSDL)

L’élément **AssociationSet** en Conceptual Schema Definition Language (CSDL) est un conteneur logique pour les instances d’association du même type. Un ensemble d'associations fournit une définition pour le regroupement d'instances d'association afin qu'elles puissent être mappées à une source de données.  

L’élément **AssociationSet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément autorisé) ;
-   End (exactement deux éléments requis) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

L’attribut **Association** spécifie le type d’association contenu dans un ensemble d’associations. Les jeux d’entités qui composent les terminaisons d’un ensemble d’associations sont spécifiés avec exactement deux éléments **finaux** enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **AssociationSet** .

| Nom de l'attribut  | Est obligatoire | Valeur                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**        | Oui         | Nom du jeu d'entités. La valeur de l’attribut **Name** ne peut pas être la même que la valeur de l’attribut **Association** .                                 |
| **Association** | Oui         | Nom qualifié complet de l'association dont l'ensemble d'associations contient des instances. L'association doit être dans le même espace de noms que l'ensemble d'associations. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **AssociationSet** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** avec deux éléments **AssociationSet** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a>Élément CollectionType (CSDL)

L’élément **CollectionType** en Conceptual Schema Definition Language (CSDL) spécifie qu’un paramètre de fonction ou un type de retour de fonction est une collection. L’élément **CollectionType** peut être un enfant de l’élément parameter ou de l’élément ReturnType (Function). Le type de collection peut être spécifié à l’aide de l’attribut **type** ou de l’un des éléments enfants suivants :

-   **CollectionType ;**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> Un modèle ne sera pas validé si le type d’une collection est spécifié avec l’attribut **type** et un élément enfant.

 

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **CollectionType** . Notez que les attributs **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**et **collation** sont uniquement applicables aux collections de **EDMSimpleTypes**.

| Nom de l'attribut                                                          | Est obligatoire | Valeur                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                                | Non          | Type de la collection.                                                                                                                                                                                                      |
| **Nullable**                                                            | Non          | **True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null. <br/> [!NOTE]                                                                                                                 |
| > dans le CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` . |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | Non          | Valeur par défaut de la propriété.                                                                                                                                                                                               |
| **MaxLength**                                                           | Non          | Longueur maximale de la valeur de propriété.                                                                                                                                                                                        |
| **Multiple**                                                         | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.                                                                                                                           |
| **Précision**                                                           | Non          | Précision de la valeur de propriété.                                                                                                                                                                                             |
| **Mettre à l'échelle**                                                               | Non          | Échelle de la valeur de propriété.                                                                                                                                                                                                 |
| **SRID**                                                                | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux.Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) |
| **Unicode**                                                             | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.                                                                                                                                |
| **Classement**                                                           | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                    |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de types d’entité **Person** (comme spécifié avec l’attribut **ElementType** ).

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

L’exemple suivant montre une fonction définie par modèle qui utilise l’élément **CollectionType** pour spécifier que la fonction accepte comme paramètre une collection de types d’entités **Department** .

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a>ComplexType, élément (CSDL)

Un élément **complexType** définit une structure de données composée de propriétés **type EDMSimpleType** ou d’autres types complexes.Un type complexe peut être une propriété d'un type d'entité ou d'un autre type complexe. Un type complexe est semblable à un type d'entité du fait qu'un type complexe définit des données. Toutefois, il existe des différences clés entre les types complexes et les types d'entités :

-   Les types complexes n'ont pas d'identité (ni de clés), par conséquent, ils ne peuvent pas exister indépendamment. Les types complexes peuvent uniquement exister en tant que propriétés de types d'entités ou d'autres types complexes.
-   Les types complexes ne peuvent pas participer à des associations. Aucune terminaison d'association ne peut être un type complexe ; par conséquent, il n'est pas possible de définir des propriétés de navigation pour des types complexes.
-   Une propriété de type complexe ne peut pas avoir de valeur NULL, bien que les propriétés scalaires d'un type complexe puissent chacune être définie sur NULL.

Un élément **complexType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Property (zéro, un ou plusieurs éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **complexType** .

| Nom de l'attribut                                                                                                 | Est obligatoire | Valeur                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom                                                                                                           | Oui         | Nom du type complexe. Le nom d'un type complexe ne peut pas être identique au nom d'un autre type complexe, d'un type d'entité ou d'une association qui figure dans l'étendue du modèle. |
| BaseType                                                                                                       | Non          | Nom d'un autre type complexe qui est le type de base du type complexe en cours de définition. <br/> [!NOTE]                                                                     |
| > cet attribut n’est pas applicable dans CSDL v1. L'héritage pour les types complexes n'est pas pris en charge dans cette version. |             |                                                                                                                                                                                     |
| Résumé                                                                                                       | Non          | **True** ou **false** (valeur par défaut), selon que le type complexe est un type abstrait. <br/> [!NOTE]                                                                  |
| > cet attribut n’est pas applicable dans CSDL v1. Les types complexes dans cette version ne peuvent pas être des types abstraits.         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **complexType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un type complexe, **Address**, avec les **EdmSimpleType** propriétés type EDMSimpleType **StreetAddress**, **City**, **StateOrProvince**, **Country**et **PostalCode**.

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

Pour définir l' **adresse** de type complexe (ci-dessus) en tant que propriété d’un type d’entité, vous devez déclarer le type de propriété dans la définition du type d’entité. L’exemple suivant illustre la propriété **Address** en tant que type complexe sur un type d’entité (**éditeur**) :

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression, élément (CSDL)

L’élément **DefiningExpression** en Conceptual Schema Definition Language (CSDL) contient une expression Entity SQL qui définit une fonction dans le modèle conceptuel.  

> [!NOTE]
> À des fins de validation, un élément **DefiningExpression** peut contenir du contenu arbitraire. Toutefois, Entity Framework lèvera une exception au moment de l’exécution si un élément **DefiningExpression** ne contient pas d’Entity SQL valide.

 

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **DefiningExpression** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant utilise un élément **DefiningExpression** pour définir une fonction qui retourne le nombre d’années écoulées depuis la publication d’un livre. Le contenu de l’élément **DefiningExpression** est écrit en Entity SQL.

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent, élément (CSDL)

L’élément **dépendant** en Conceptual Schema Definition Language (CSDL) est un élément enfant de l’élément ReferentialConstraint et définit la terminaison dépendante d’une contrainte référentielle. Un élément **ReferentialConstraint** définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle. De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité. Le type d’entité référencé est appelé *terminaison principale* de la contrainte. Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte. Les éléments **PropertyRef** sont utilisés pour spécifier les clés qui référencent la terminaison principale.

L’élément **dépendant** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   PropertyRef (un ou plusieurs éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **dépendant** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rôle**       | Oui         | Nom du type d'entité au niveau de la terminaison dépendante de l'association. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **dépendant** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **ReferentialConstraint** utilisé dans le cadre de la définition de l’Association **PublishedBy** . La propriété **PublisherId** du type **d’entité Book** compose la terminaison dépendante de la contrainte référentielle.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a>Documentation, élément (CSDL)

L’élément **documentation** en Conceptual Schema Definition Language (CSDL) peut être utilisé pour fournir des informations sur un objet défini dans un élément parent. Dans un fichier. edmx, lorsque l’élément **documentation** est un enfant d’un élément qui apparaît en tant qu’objet sur l’aire de conception du concepteur EF (tel qu’une entité, une association ou une propriété), le contenu de l’élément **documentation** s’affiche dans la fenêtre **Propriétés** de Visual Studio pour l’objet.

L’élément **documentation** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   **Summary**: brève description de l’élément parent. (zéro ou un élément).
-   **LongDescription**: description complète de l’élément parent. (zéro ou un élément).
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **documentation** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément de **documentation** en tant qu’élément enfant d’un élément EntityType. Si l’extrait de code ci-dessous se trouve dans le contenu CSDL d’un fichier. edmx, le contenu des éléments **Summary** et **LongDescription** apparaît dans la fenêtre **Propriétés** de Visual Studio lorsque vous cliquez sur le `Customer` type d’entité.

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a>End, élément (CSDL)

L’élément **end** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément Association ou de l’élément AssociationSet. Dans chaque cas, le rôle de l’élément de **fin** est différent et les attributs applicables sont différents.

### <a name="end-element-as-a-child-of-the-association-element"></a>Élément End comme enfant de l'élément Association

Un élément **end** (en tant qu’enfant de l’élément **Association** ) identifie le type d’entité à une terminaison d’une association et le nombre d’instances de type d’entité qui peuvent exister à cette terminaison d’une association. Les terminaisons d'association sont définies dans le cadre d'une association ; une association doit avoir exactement deux terminaisons d'association. Il est possible d'accéder aux instances de type d'entité au niveau de la terminaison d'une association via les propriétés de navigation ou les clés étrangères si elles sont exposées sur un type d'entité.  

Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   OnDelete (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **final** lorsqu’il est l’enfant d’un élément **Association** .

| Nom de l'attribut   | Est obligatoire | Valeur                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | Oui         | Nom du type d'entité au niveau de la terminaison de l'association.                                                                                                                                                                                                                                                                                                                                                         |
| **Rôle**         | Non          | Nom de la terminaison de l'association. Si aucun nom n'est fourni, le nom du type d'entité au niveau de la terminaison de l'association sera utilisé.                                                                                                                                                                                                                                                                                           |
| **Multiplicité** | Oui         | **1**, **0.. 1**, ou **\*** selon le nombre d’instances de type d’entité qui peuvent être à la fin de l’Association. <br/> **1** indique qu’il existe exactement une instance de type d’entité au niveau de la terminaison d’association. <br/> **0.. 1** indique que zéro ou une instance de type d’entité existe au niveau de la terminaison d’association. <br/> **\*** indique qu’il existe zéro, une ou plusieurs instances de type d’entité au niveau de la terminaison d’association. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** . Les valeurs de **multiplicité** pour chaque **terminaison** de l’Association indiquent que de nombreuses **commandes** peuvent être associées à un **client**, mais qu’un seul **client** peut être associé à une **commande**. En outre, l’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext sont supprimées si le **client** est supprimé.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Élément End comme enfant de l'élément AssociationSet

L’élément **end** spécifie une extrémité d’un ensemble d’associations. L’élément **AssociationSet** doit contenir deux éléments **end** . Les informations contenues dans un élément **end** sont utilisées dans le mappage d’un ensemble d’associations à une source de données.

Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

> [!NOTE]
> Les éléments d'annotation doivent figurer après tous les autres éléments enfants. Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.

 

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **end** lorsqu’il est l’enfant d’un élément **AssociationSet** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet ;**  | Oui         | Nom de l’élément **EntitySet** qui définit une extrémité de l’élément **AssociationSet** parent. L’élément **EntitySet** doit être défini dans le même conteneur d’entités que l’élément **AssociationSet** parent. |
| **Rôle**       | Non          | Nom de la terminaison de l'ensemble d'associations. Si l’attribut **role** n’est pas utilisé, le nom de la terminaison de l’ensemble d’associations sera le nom du jeu d’entités.                                                                   |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** avec deux éléments **AssociationSet** , chacun avec deux éléments **end** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer, élément (CSDL)

L’élément **EntityContainer** en Conceptual Schema Definition Language (CSDL) est un conteneur logique pour les jeux d’entités, les ensembles d’associations et les importations de fonction. Un conteneur d'entités de modèle conceptuel est mappé à un conteneur d'entités de modèle de stockage via l'élément EntityContainerMapping. Un conteneur d'entités de modèle de stockage décrit la structure de la base de données : les jeux d'entités décrivent les tables, les ensembles d'associations décrivent les contraintes de clé étrangère et les importations de fonction décrivent les procédures stockées dans une base de données.

Un élément **EntityContainer** peut avoir zéro ou un élément documentation. Si un élément de **documentation** est présent, il doit précéder tous les éléments **EntitySet**, **AssociationSet**et **FunctionImport** .

Un élément **EntityContainer** peut avoir zéro ou plusieurs des éléments enfants suivants (dans l’ordre indiqué) :

-   EntitySet ;
-   AssociationSet ;
-   FunctionImport
-   éléments d'annotation.

Vous pouvez étendre un élément **EntityContainer** pour inclure le contenu d’un autre **EntityContainer** qui se trouve dans le même espace de noms. Pour inclure le contenu d’un autre **EntityContainer**, dans l’élément **EntityContainer** de référence, définissez la valeur de l’attribut **extends** sur le nom de l’élément **EntityContainer** que vous souhaitez inclure. Tous les éléments enfants de l’élément **EntityContainer** inclus sont traités comme des éléments enfants de l’élément **EntityContainer** de référence.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **using** .

| Nom de l'attribut | Est obligatoire | Valeur                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nom**       | Oui         | Nom du conteneur d'entités.                               |
| **Dure**    | Non          | Le nom d'un autre conteneur d'entités au sein du même espace de noms. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityContainer** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** qui définit trois jeux d’entités et deux ensembles d’associations.

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a>EntitySet, élément (CSDL)

L’élément **EntitySet** dans Conceptual Schema Definition Language est un conteneur logique pour les instances d’un type d’entité et les instances de tout type dérivé de ce type d’entité. La relation entre un type d'entité et un jeu d'entités est analogue à la relation entre une ligne et une table dans une base de données relationnelle. Comme une ligne, un type d'entité définit un jeu de données connexes et, comme une table, un jeu d'entités contient des instances de cette définition. Un jeu d'entités fournit une construction pour le regroupement d'instances du type d'entité, afin qu'elles puissent être mappées aux structures de données associées dans une source de données.  

Plusieurs jeux d'entités peuvent être définis pour un type d'entité particulier.

> [!NOTE]
> Le concepteur EF ne prend pas en charge les modèles conceptuels qui contiennent plusieurs jeux d’entités par type.

 

L’élément **EntitySet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   élément Documentation (zéro ou un élément autorisé) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntitySet** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom du jeu d'entités.                                                              |
| **EntityType** | Oui         | Nom qualifié complet du type d'entité pour lequel le jeu d'entités contient des instances. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntitySet** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainer** avec trois éléments **EntitySet** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

Il est possible de définir des jeux d'entités multiples par type (MEST). L’exemple suivant définit un conteneur d’entités avec deux jeux d’entités pour le type **d’entité Book** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a>EntityType, élément (CSDL)

L’élément **EntityType** représente la structure d’un concept de niveau supérieur, tel qu’un client ou une commande, dans un modèle conceptuel. Un type d'entité est un modèle pour des instances de types d'entités dans une application. Chaque modèle contient les informations suivantes :

-   Nom unique. (Obligatoire.)
-   Clé d'entité définie par une ou plusieurs propriétés. (Obligatoire.)
-   Propriétés pour contenir des données. (Facultatif.)
-   Propriétés de navigation qui permettent de naviguer d'une terminaison d'une association à l'autre terminaison. (Facultatif.)

Dans une application, une instance d'un type d'entité représente un objet spécifique (tel qu'un client ou une commande spécifique). Chaque instance d'un type d'entité doit avoir une clé d'entité unique dans un jeu d'entités.

Deux instances de type d'entité sont considérées égales seulement si elles sont du même type et si les valeurs de leurs clés d'entité sont identiques.

Un élément **EntityType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Key (zéro ou un élément) ;
-   Property (zéro, un ou plusieurs éléments) ;
-   NavigationProperty (zéro, un ou plusieurs éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityType** .

| Nom de l'attribut                                                                                                                                  | Est obligatoire | Valeur                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nom**                                                                                                                                        | Oui         | Le nom du type d’entité.                                                                     |
| **BaseType**                                                                                                                                    | Non          | Le nom d’un autre type d’entité qui est le type de base du type d’entité défini.  |
| **Résumé**                                                                                                                                    | Non          | **True** ou **false**, selon que le type d’entité est un type abstrait ou non.                 |
| **OpenType**                                                                                                                                    | Non          | **True** ou **false** selon que le type d’entité est un type d’entité ouvert. <br/> [!NOTE] |
| > l’attribut **OpenType** s’applique uniquement aux types d’entité définis dans les modèles conceptuels utilisés avec ADO.NET Data Services. |             |                                                                                                  |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec trois éléments de **propriété** et deux éléments **NavigationProperty** :

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a>EnumType, élément (CSDL)

L’élément **enumType** représente un type énuméré.

Un élément **enumType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Membre (zéro, un ou plusieurs éléments)
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **enumType** .

| Nom de l'attribut     | Est obligatoire | Valeur                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**           | Oui         | Le nom du type d’entité.                                                                                                                                                                  |
| **IsFlags**        | Non          | **True** ou **false**, selon que le type enum peut être utilisé comme un ensemble d’indicateurs. La valeur par défaut est **false.**                                                                     |
| **UnderlyingType** | Non          | **Edm. Byte**, **Edm. Int16**, **Edm. Int32**, **Edm. Int64** ou **Edm. SByte** définissant la plage de valeurs du type. Le type sous-jacent par défaut des éléments d’énumération est **Edm. Int32.**. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **enumType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **enumType** avec trois éléments **membres** :

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function, élément (CSDL)

L’élément de **fonction** en Conceptual Schema Definition Language (CSDL) est utilisé pour définir ou déclarer des fonctions dans le modèle conceptuel. Une fonction est définie à l'aide d'un élément DefiningExpression.  

Un élément **Function** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Parameter (zéro, un ou plusieurs éléments) ;
-   DefiningExpression (zéro ou un élément) ;
-   ReturnType (Function) (zéro ou un élément)
-   éléments d'annotation (zéro, un ou plusieurs éléments).

Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** (Function) ou **ReturnType** (voir ci-dessous), mais pas les deux. Les types de retour possibles correspondent à tout type EdmSimpleType, type d'entité, type complexe, type de ligne ou type REF (ou à une collection de l'un de ces types).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Function** .

| Nom de l'attribut | Est obligatoire | Valeur                              |
|:---------------|:------------|:-----------------------------------|
| **Nom**       | Oui         | Nom de la fonction.          |
| **ReturnType** | Non          | Type retourné par la fonction. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fonction** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant utilise un élément **Function** pour définir une fonction qui retourne le nombre d’années écoulées depuis l’embauche d’un formateur.

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport, élément (CSDL)

L’élément **FunctionImport** en Conceptual Schema Definition Language (CSDL) représente une fonction qui est définie dans la source de données, mais qui est disponible pour les objets via le modèle conceptuel. Par exemple, un élément Function dans le modèle de stockage peut être utilisé pour représenter une procédure stockée dans une base de données. Un élément **FunctionImport** du modèle conceptuel représente la fonction correspondante dans une Entity Framework application et est mappée à la fonction de modèle de stockage à l’aide de l’élément FunctionImportMapping. Lorsque la fonction est appelée dans l'application, la procédure stockée correspondante est exécutée dans la base de données.

L’élément **FunctionImport** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément autorisé) ;
-   Parameter (zéro, un ou plusieurs éléments autorisés) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).
-   ReturnType (FunctionImport) (zéro, un ou plusieurs éléments autorisés)

Un élément de **paramètre** doit être défini pour chaque paramètre que la fonction accepte.

Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** (FunctionImport) ou **ReturnType** (voir ci-dessous), mais pas les deux. La valeur du type de retour doit être une collection de type EDMSimpleType, d’EntityType ou de ComplexType.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **FunctionImport** .

| Nom de l'attribut   | Est obligatoire | Valeur                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**         | Oui         | Nom de la fonction importée.                                                                                                                                                                    |
| **ReturnType**   | Non          | Type retourné par la fonction. N’utilisez pas cet attribut si la fonction ne renvoie pas de valeur. Dans le cas contraire, la valeur doit être une collection de ComplexType, EntityType ou type EDMSimpleType.        |
| **EntitySet ;**    | Non          | Si la fonction retourne une collection de types d’entités, la valeur de l' **EntitySet** doit être le jeu d’entités auquel la collection appartient. Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé. |
| **IsComposable** | Non          | Si la valeur est définie sur true, la fonction est composable (fonction table) et peut être utilisée dans une requête LINQ.La valeur par défaut est **false**.                                                           |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **FunctionImport** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **FunctionImport** qui accepte un paramètre et retourne une collection de types d’entités :

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Key, élément (CSDL)

L’élément **Key** est un élément enfant de l’élément EntityType et définit une *clé d’entité* (une propriété ou un ensemble de propriétés d’un type d’entité qui détermine l’identité). Les propriétés qui composent une clé d'entité sont choisies au moment du design. Les valeurs des propriétés de clé d'entité doivent identifier de façon unique une instance de type d'entité dans un jeu d'entités au moment de l'exécution. Les propriétés qui composent une clé d'entité doivent être choisies pour garantir l'unicité des instances dans un jeu d'entités. L’élément **Key** définit une clé d’entité en référençant une ou plusieurs des propriétés d’un type d’entité.

L’élément **Key** peut avoir les éléments enfants suivants :

-   PropertyRef (un ou plusieurs éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Key** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple ci-dessous définit un type **d'** entité nommé Book. La clé d’entité est définie en référençant la propriété **ISBN** du type d’entité.

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

La propriété **ISBN** est un bon choix pour la clé d’entité, car un numéro ISBN (International Standard Book Number) identifie de manière unique un livre.

L’exemple suivant montre un type d’entité (**auteur**) qui a une clé d’entité composée de deux propriétés, **nom** et **adresse**.

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

L’utilisation du **nom** et de l' **adresse** pour la clé d’entité est un choix raisonnable, car il est peu probable que deux auteurs du même nom vivent à la même adresse. Toutefois, ce choix pour une clé d'entité ne garantit pas vraiment l'unicité des clés d'entité dans un jeu d'entités. L’ajout d’une **propriété, telle**que la réutilisation, qui pourrait être utilisée pour identifier un auteur de manière unique, est recommandé dans ce cas.

 

## <a name="member-element-csdl"></a>Member, élément (CSDL)

L’élément **member** est un élément enfant de l’élément enumType et définit un membre du type énuméré.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **FunctionImport** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom du membre.                                                                                                                                                                  |
| **Valeur**      | Non          | Valeur du membre. Par défaut, le premier membre a la valeur 0, et la valeur de chaque énumérateur successif est incrémentée de 1. Plusieurs membres ayant les mêmes valeurs peuvent exister. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **FunctionImport** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **enumType** avec trois éléments **membres** :

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty, élément (CSDL)

Un élément **NavigationProperty** définit une propriété de navigation, qui fournit une référence à l’autre terminaison d’une association. Contrairement aux propriétés définies à l'aide de l'élément Property, les propriétés de navigation ne définissent pas la forme et les caractéristiques des données. Elles fournissent un moyen d'explorer une association entre deux types d'entités.

Notez que les propriétés de navigation sont facultatives sur les deux types d'entités au niveau des terminaisons d'une association. Si vous définissez une propriété de navigation sur un type d'entité au niveau de la terminaison d'une association, il n'est pas nécessaire de définir une propriété de navigation sur le type d'entité à l'autre terminaison de l'association.

Le type de données retourné par une propriété de navigation est déterminé par la multiplicité de sa terminaison d'association distante. Par exemple, supposons qu’une propriété de navigation, **OrdersNavProp**, existe sur un type d’entité **Customer** et navigue vers une association un-à-plusieurs entre **Customer** et **Order**. Étant donné que la terminaison d’association distante pour la propriété de navigation possède la multiplicité many ( \* ), son type de données est une collection (of **Order**). De même, si une propriété de navigation, **CustomerNavProp**, existe sur le type d’entité **Order** , son type de données est **Customer** , car la multiplicité de la terminaison distante est un (1).

Un élément **NavigationProperty** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **NavigationProperty** .

| Nom de l'attribut   | Est obligatoire | Valeur                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**         | Oui         | Nom de la propriété de navigation.                                                                                                                                                                                                             |
| **Relation** | Oui         | Nom d'une association figurant dans l'étendue du modèle.                                                                                                                                                                                |
| **ToRole**       | Oui         | Terminaison de l'association à laquelle la navigation prend fin. La valeur de l’attribut **ToRole** doit être identique à la valeur de l’un des attributs de **rôle** définis sur l’une des terminaisons d’Association (définie dans l’élément AssociationEnd).       |
| **FromRole**     | Oui         | Terminaison de l'association où la navigation commence. La valeur de l’attribut **FromRole** doit être identique à la valeur de l’un des attributs de **rôle** définis sur l’une des terminaisons d’Association (définie dans l’élément AssociationEnd). |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **NavigationProperty** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant définit un type d’entité (**Book**) avec deux propriétés de navigation (**PublishedBy** et **WrittenBy**) :

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a>OnDelete, élément (CSDL)

L’élément **OnDelete** en Conceptual Schema Definition Language (CSDL) définit le comportement qui est connecté avec une association. Si l’attribut **action** est défini sur **cascade** à une terminaison d’une association, les types d’entités associés à l’autre terminaison de l’Association sont supprimés lorsque le type d’entité de la première terminaison est supprimé. Si l’association entre deux types d’entités est une relation clé primaire-clé primaire, un objet dépendant chargé est supprimé lorsque l’objet principal de l’autre terminaison de l’Association est supprimé, quelle que soit la spécification de **OnDelete** .  

> [!NOTE]
> L’élément **OnDelete** affecte uniquement le comportement au moment de l’exécution d’une application ; elle n’affecte pas le comportement dans la source de données. Le comportement défini dans la source de données doit être le même que le comportement défini dans l'application.

 

Un élément **OnDelete** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **OnDelete** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Action**     | Oui         | **Cascade** ou **None**. Si vous disposez d’une **cascade**, les types d’entités dépendants sont supprimés lorsque le type d’entité principal est supprimé. Si **aucune**valeur n’est, les types d’entités dépendants ne sont pas supprimés lorsque le type d’entité principal est supprimé. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** . L’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext seront supprimées lors de la suppression du **client** .

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter, élément (CSDL)

L’élément **Parameter** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément FunctionImport ou de l’élément Function.

### <a name="functionimport-element-application"></a>Application de l'élément FunctionImport

Un élément **Parameter** (en tant qu’enfant de l’élément **FunctionImport** ) est utilisé pour définir les paramètres d’entrée et de sortie des importations de fonctions déclarées dans le langage CSDL.

L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément autorisé) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom du paramètre.                                                                                                                                                                                                      |
| **Type**       | Oui         | Le type du paramètre. La valeur doit être de type **EDMSimpleType** ou de type complexe, dans la portée du modèle.                                                                                                             |
| **Mode**       | Non          | **In**, **out**ou **INOUT** selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie.                                                                                                                |
| **MaxLength**  | Non          | La longueur maximale autorisée du paramètre.                                                                                                                                                                                    |
| **Précision**  | Non          | La précision du paramètre.                                                                                                                                                                                                 |
| **Mettre à l'échelle**      | Non          | L’échelle du paramètre.                                                                                                                                                                                                     |
| **SRID**       | Non          | Identificateur de référence système spatial. Valide uniquement pour les paramètres des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **FunctionImport** avec un élément enfant **Parameter** . La fonction accepte un paramètre d'entrée et retourne une collection de types d'entités.

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Application de l'élément Function

Un élément **Parameter** (en tant qu’enfant de l’élément **Function** ) définit des paramètres pour les fonctions qui sont définies ou déclarées dans un modèle conceptuel.

L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   CollectionType (zéro ou un élément) ;
-   ReferenceType (zéro ou un élément) ;
-   RowType (zéro ou un élément) ;

> [!NOTE]
> Seul l’un des éléments **CollectionType**, **ReferenceType**ou **RowType** peut être un élément enfant d’un élément de **propriété** .

 

-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

> [!NOTE]
> Les éléments d'annotation doivent figurer après tous les autres éléments enfants. Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.

 

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .

| Nom de l'attribut   | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**         | Oui         | Nom du paramètre.                                                                                                                                                                                                      |
| **Type**         | Non          | Le type du paramètre. Un paramètre peut correspondre à l'un quelconque des types suivants (ou à des collections de ces types) : <br/> **Type EDMSimpleType** <br/> type d'entité <br/> type complexe <br/> type de ligne <br/> type référence                             |
| **Nullable**     | Non          | **True** (valeur par défaut) ou **false** selon que la propriété peut avoir une valeur **null** ou non.                                                                                                                          |
| **DefaultValue** | Non          | Valeur par défaut de la propriété.                                                                                                                                                                                              |
| **MaxLength**    | Non          | Longueur maximale de la valeur de propriété.                                                                                                                                                                                       |
| **Multiple**  | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.                                                                                                                          |
| **Précision**    | Non          | Précision de la valeur de propriété.                                                                                                                                                                                            |
| **Mettre à l'échelle**        | Non          | Échelle de la valeur de propriété.                                                                                                                                                                                                |
| **SRID**         | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**      | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.                                                                                                                               |
| **Classement**    | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                   |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **Function** qui utilise un élément enfant **Parameter** pour définir un paramètre de fonction.

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Principal, élément (CSDL)

L’élément **principal** en Conceptual Schema Definition Language (CSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison principale d’une contrainte référentielle. Un élément **ReferentialConstraint** définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle. De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité. Le type d’entité référencé est appelé *terminaison principale* de la contrainte. Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte. Les éléments **PropertyRef** sont utilisés pour spécifier les clés qui sont référencées par la terminaison dépendante.

L’élément **principal** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   PropertyRef (un ou plusieurs éléments) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **principal** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rôle**       | Oui         | Nom du type d'entité au niveau de la terminaison principale de l'association. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **principal** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **ReferentialConstraint** qui fait partie de la définition de l’Association **PublishedBy** . La propriété **ID** du type d’entité du serveur de **publication** compose la terminaison principale de la contrainte référentielle.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a>Property, élément (CSDL)

L’élément de **propriété** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément EntityType, de l’élément complexType ou de l’élément RowType.

### <a name="entitytype-and-complextype-element-applications"></a>Applications des éléments EntityType et ComplexType

Les éléments de **propriété** (en tant qu’enfants des éléments **EntityType** ou **complexType** ) définissent la forme et les caractéristiques des données qu’une instance de type d’entité ou une instance de type complexe contiendra. Les propriétés dans un modèle conceptuel sont analogues aux propriétés qui sont définies sur une classe. De même que les propriétés sur une classe définissent la forme de la classe et acheminent des informations sur les objets, les propriétés dans un modèle conceptuel définissent la forme d'un type d'entité et acheminent des informations sur les instances de type d'entité.

L’élément **Property** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   élément Documentation (zéro ou un élément autorisé) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

Les facettes suivantes peuvent être appliquées à un élément **Property** : **Nullable**, **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**, **collation**, **ConcurrencyMode**. Les facettes sont des attributs XML qui fournissent des informations sur la manière dont les valeurs de propriété sont stockées dans la banque de données.

> [!NOTE]
> Les facettes ne peuvent être appliquées qu’à des propriétés de type **type EDMSimpleType**.

 

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .

| Nom de l'attribut                                                         | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**                                                               | Oui         | Nom de la propriété.                                                                                                                                                                                                       |
| **Type**                                                               | Oui         | Type de la valeur de propriété. Le type de la valeur de propriété doit être un type **EDMSimpleType** ou un type complexe (indiqué par un nom qualifié complet) qui se trouve dans la portée du modèle.                                                 |
| **Nullable**                                                           | Non          | **True** (valeur par défaut) ou <strong>False</strong> selon que la propriété peut avoir ou non une valeur null. <br/> [!NOTE]                                                                                                   |
| > dans le langage CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | Non          | Valeur par défaut de la propriété.                                                                                                                                                                                              |
| **MaxLength**                                                          | Non          | Longueur maximale de la valeur de propriété.                                                                                                                                                                                       |
| **Multiple**                                                        | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.                                                                                                                          |
| **Précision**                                                          | Non          | Précision de la valeur de propriété.                                                                                                                                                                                            |
| **Mettre à l'échelle**                                                              | Non          | Échelle de la valeur de propriété.                                                                                                                                                                                                |
| **SRID**                                                               | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                            | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.                                                                                                                               |
| **Classement**                                                          | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                   |
| **ConcurrencyMode**                                                    | Non          | **None** (valeur par défaut) ou **Fixed**. Si la valeur est définie sur **Fixed**, la valeur de propriété sera utilisée dans les contrôles d’accès concurrentiel optimiste.                                                                                  |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec trois éléments **Property** :

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

L’exemple suivant montre un élément **complexType** avec cinq éléments de **propriété** :

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>Application de l'élément RowType

Les éléments de **propriété** (comme les enfants d’un élément **RowType** ) définissent la forme et les caractéristiques des données qui peuvent être passées ou retournées à partir d’une fonction définie par modèle.  

L’élément **Property** peut avoir exactement l’un des éléments enfants suivants :

-   CollectionType ;
-   ReferenceType
-   RowType

L’élément **Property** peut avoir n’importe quel nombre d’éléments d’annotation enfants.

> [!NOTE]
> Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.

 

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .

| Nom de l'attribut                                                     | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**                                                           | Oui         | Nom de la propriété.                                                                                                                                                                                                       |
| **Type**                                                           | Oui         | Type de la valeur de propriété.                                                                                                                                                                                                 |
| **Nullable**                                                       | Non          | **True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null. <br/> [!NOTE]                                                                                                                |
| > dans CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | Non          | Valeur par défaut de la propriété.                                                                                                                                                                                              |
| **MaxLength**                                                      | Non          | Longueur maximale de la valeur de propriété.                                                                                                                                                                                       |
| **Multiple**                                                    | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.                                                                                                                          |
| **Précision**                                                      | Non          | Précision de la valeur de propriété.                                                                                                                                                                                            |
| **Mettre à l'échelle**                                                          | Non          | Échelle de la valeur de propriété.                                                                                                                                                                                                |
| **SRID**                                                           | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.                                                                                                                               |
| **Classement**                                                      | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                   |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

#### <a name="example"></a>Exemple

L’exemple suivant montre les éléments de **propriété** utilisés pour définir la forme du type de retour d’une fonction définie par modèle.

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef, élément (CSDL)

L’élément **PropertyRef** en Conceptual Schema Definition Language (CSDL) fait référence à une propriété d’un type d’entité pour indiquer que la propriété effectuera l’un des rôles suivants :

-   Partie de la clé de l'entité (une propriété ou un jeu de propriétés d'un type d'entité qui détermine l'identité). Un ou plusieurs éléments **PropertyRef** peuvent être utilisés pour définir une clé d’entité.
-   Terminaison dépendante ou principale d'une contrainte référentielle.

L’élément **PropertyRef** peut uniquement comporter des éléments d’annotation (zéro ou plus) en tant qu’éléments enfants.

> [!NOTE]
> Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.

 

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **PropertyRef** .

| Nom de l'attribut | Est obligatoire | Valeur                                |
|:---------------|:------------|:-------------------------------------|
| **Nom**       | Oui         | Nom de la propriété référencée. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **PropertyRef** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple ci-dessous définit un type**d'** entité (Book). La clé d’entité est définie en référençant la propriété **ISBN** du type d’entité.

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

Dans l’exemple suivant, deux éléments **PropertyRef** sont utilisés pour indiquer que deux propriétés (**ID** et **PublisherId**) sont les terminaisons principale et dépendante d’une contrainte référentielle.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a>Élément ReferenceType (CSDL)

L’élément **ReferenceType** en Conceptual Schema Definition Language (CSDL) spécifie une référence à un type d’entité. L’élément **ReferenceType** peut être un enfant des éléments suivants :

-   ReturnType (fonction)
-   Paramètre
-   CollectionType ;

L’élément **ReferenceType** est utilisé lors de la définition d’un paramètre ou d’un type de retour pour une fonction.

Un élément **ReferenceType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **ReferenceType** .

| Nom de l'attribut | Est obligatoire | Valeur                                         |
|:---------------|:------------|:----------------------------------------------|
| **Type**       | Oui         | Nom du type d'entité référencé. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReferenceType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **ReferenceType** utilisé comme enfant d’un élément **Parameter** dans une fonction définie par modèle qui accepte une référence à un type d’entité **Person** :

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

L’exemple suivant montre l’élément **ReferenceType** utilisé comme enfant d’un élément **ReturnType** (Function) dans une fonction définie par modèle qui retourne une référence à un type d’entité **Person** :

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint, élément (CSDL)

Un élément **ReferentialConstraint** en Conceptual Schema Definition Language (CSDL) définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle. De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité. Le type d’entité référencé est appelé *terminaison principale* de la contrainte. Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte.

Si une clé étrangère exposée sur un type d’entité fait référence à une propriété sur un autre type d’entité, l’élément **ReferentialConstraint** définit une association entre les deux types d’entité. Étant donné que l’élément **ReferentialConstraint** fournit des informations sur la façon dont deux types d’entité sont associés, aucun élément AssociationSetMapping correspondant n’est nécessaire dans le Mapping Specification Language (MSL). Une association entre deux types d’entités qui n’ont pas de clés étrangères exposées doit avoir un élément **AssociationSetMapping** correspondant pour mapper les informations d’association à la source de données.

Si une clé étrangère n’est pas exposée sur un type d’entité, l’élément **ReferentialConstraint** ne peut définir qu’une contrainte de clé primaire-clé primaire entre le type d’entité et un autre type d’entité.

Un élément **ReferentialConstraint** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   Principal (exactement un élément) ;
-   Dépendent (exactement un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

L’élément **ReferentialConstraint** peut avoir n’importe quel nombre d’attributs d’annotation (attributs XML personnalisés). Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **ReferentialConstraint** utilisé dans le cadre de la définition de l’Association **PublishedBy** .

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType (Function), élément (CSDL)

L’élément **ReturnType** (Function) en Conceptual Schema Definition Language (CSDL) spécifie le type de retour pour une fonction définie dans un élément Function. Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .

Les types de retour peuvent être n’importe quel **type EDMSimpleType**, type d’entité, type complexe, type de ligne, type ref ou une collection de l’un de ces types.

Le type de retour d’une fonction peut être spécifié avec l’attribut de **type** de l’élément **ReturnType** (Function), ou avec l’un des éléments enfants suivants :

-   CollectionType ;
-   ReferenceType
-   RowType

> [!NOTE]
> Un modèle ne sera pas validé si vous spécifiez un type de retour de fonction avec à la fois l’attribut de **type** de l’élément **ReturnType** (Function) et l’un des éléments enfants.

 

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **ReturnType** (Function).

| Nom de l'attribut | Est obligatoire | Valeur                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | Non          | Type retourné par la fonction. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** (Function). Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant utilise un élément **Function** pour définir une fonction qui retourne le nombre d’années pendant lequel un livre a été imprimé. Notez que le type de retour est spécifié par l’attribut **type** d’un élément **ReturnType** (Function).

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport), élément (CSDL)

L’élément **ReturnType** (FunctionImport) de Conceptual Schema Definition Language (CSDL) spécifie le type de retour pour une fonction définie dans un élément FunctionImport. Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .

Les types de retour peuvent être n’importe quelle collection de type d’entité, de type complexe ou de **type EDMSimpleType**.

Le type de retour d’une fonction est spécifié avec l’attribut de **type** de l’élément **ReturnType** (FunctionImport).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **ReturnType** (FunctionImport).

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**       | Non          | Type retourné par la fonction. La valeur doit être une collection de ComplexType, EntityType ou type EDMSimpleType.                                                                                      |
| **EntitySet ;**  | Non          | Si la fonction retourne une collection de types d’entités, la valeur de l' **EntitySet** doit être le jeu d’entités auquel la collection appartient. Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** (FunctionImport). Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant utilise un **FunctionImport** qui retourne des livres et des serveurs de publication. Notez que la fonction retourne deux jeux de résultats et, par conséquent, deux éléments **ReturnType** (FunctionImport) sont spécifiés.

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>Élément RowType (CSDL)

Un élément **RowType** en Conceptual Schema Definition Language (CSDL) définit une structure sans nom en tant que paramètre ou type de retour pour une fonction définie dans le modèle conceptuel.

Un élément **RowType** peut être l’enfant des éléments suivants :

-   CollectionType ;
-   Paramètre
-   ReturnType (fonction)

Un élément **RowType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Property (un ou plusieurs) ;
-   éléments d'annotation (zéro, un ou plusieurs).

### <a name="applicable-attributes"></a>Attributs applicables

Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **RowType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

### <a name="example"></a>Exemple

L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a>Schema, élément (CSDL)

L’élément **Schema** est l’élément racine d’une définition de modèle conceptuel. Il contient des définitions pour les objets, fonctions et conteneurs qui composent un modèle conceptuel.

L’élément **Schema** peut contenir zéro, un ou plusieurs des éléments enfants suivants :

-   Utilisation
-   EntityContainer
-   EntityType
-   EnumType
-   Association
-   ComplexType
-   Fonction

Un élément de **schéma** peut contenir zéro ou un élément d’annotation.

> [!NOTE]
> L’élément de **fonction** et les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.

 

L’élément **Schema** utilise l’attribut **namespace** pour définir l’espace de noms pour le type d’entité, le type complexe et les objets Association dans un modèle conceptuel. Dans un espace de noms, deux objets ne peuvent pas avoir le même nom. Les espaces de noms peuvent s’étendre sur plusieurs éléments de **schéma** et plusieurs fichiers. csdl.

Un espace de noms de modèle conceptuel est différent de l’espace de noms XML de l’élément de **schéma** . Un espace de noms de modèle conceptuel (tel que défini par l’attribut **namespace** ) est un conteneur logique pour les types d’entité, les types complexes et les types d’association. L’espace de noms XML (indiqué par l’attribut **xmlns** ) d’un élément de **schéma** est l’espace de noms par défaut pour les éléments enfants et les attributs de l’élément de **schéma** . Les espaces de noms XML de la forme https://schemas.microsoft.com/ado/YYYY/MM/edm (où aaaa et mm représentent respectivement une année et un mois) sont réservés au langage CSDL. Des éléments et attributs personnalisés ne peuvent pas être dans des espaces de noms de cette forme.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Schema** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espace de noms**  | Oui         | Espace de noms du modèle conceptuel. La valeur de l’attribut d' **espace de noms** est utilisée pour former le nom qualifié complet d’un type. Par exemple, si un **EntityType** nommé *Customer* est dans l’espace de noms simple. example. Model, le nom qualifié complet de l' **EntityType** est SimpleExampleModel. Customer. <br/> Les chaînes suivantes ne peuvent pas être utilisées comme valeur pour l’attribut d' **espace de noms** : **System**, **transient**ou **EDM**. La valeur de l’attribut d' **espace de noms** ne peut pas être la même que la valeur de l’attribut d' **espace de noms** dans l’élément de schéma SSDL. |
| **Alias**      | Non          | Identificateur utilisé à la place du nom de l'espace de noms. Par exemple, si un **EntityType** nommé *Customer* est dans l’espace de noms simple. example. Model et que la valeur de l’attribut **alias** est *Model*, vous pouvez utiliser Model. Customer comme nom qualifié complet de l' **EntityType.**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **schéma** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant illustre un élément de **schéma** qui contient un élément **EntityContainer** , deux éléments **EntityType** et un élément **Association** .

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a>Élément TypeRef (CSDL)

L’élément **TypeRef** en Conceptual Schema Definition Language (CSDL) fournit une référence à un type nommé existant. L’élément **TypeRef** peut être un enfant de l’élément CollectionType, qui est utilisé pour spécifier qu’une fonction a une collection en tant que paramètre ou type de retour.

Un élément **TypeRef** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   Documentation (zéro ou un élément) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **TypeRef** . Notez que les attributs **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**et **collation** sont uniquement applicables à **EDMSimpleTypes**.

| Nom de l'attribut                                                     | Est obligatoire | Valeur                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                           | Non          | Nom du type référencé.                                                                                                                                                                                          |
| **Nullable**                                                       | Non          | **True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null. <br/> [!NOTE]                                                                                                                |
| > dans CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | Non          | Valeur par défaut de la propriété.                                                                                                                                                                                              |
| **MaxLength**                                                      | Non          | Longueur maximale de la valeur de propriété.                                                                                                                                                                                       |
| **Multiple**                                                    | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.                                                                                                                          |
| **Précision**                                                      | Non          | Précision de la valeur de propriété.                                                                                                                                                                                            |
| **Mettre à l'échelle**                                                          | Non          | Échelle de la valeur de propriété.                                                                                                                                                                                                |
| **SRID**                                                           | Non          | Identificateur de référence système spatial. Valide uniquement pour les propriétés des types spatiaux. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | Non          | **True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.                                                                                                                               |
| **Classement**                                                      | Non          | Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.                                                                                                                                                   |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant montre une fonction définie par modèle qui utilise l’élément **TypeRef** (en tant qu’enfant d’un élément **CollectionType** ) pour spécifier que la fonction accepte une collection de types d’entités **Department** .

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a>Using, élément (CSDL)

L’élément **using** de Conceptual Schema Definition Language (CSDL) importe le contenu d’un modèle conceptuel qui existe dans un espace de noms différent. En définissant la valeur de l’attribut d' **espace de noms** , vous pouvez faire référence à des types d’entités, des types complexes et des types d’associations définis dans un autre modèle conceptuel. Plus d’un élément **using** peut être un enfant d’un élément **Schema** .

> [!NOTE]
> L’élément **using** dans CSDL ne fonctionne pas exactement comme une instruction **using** dans un langage de programmation. En important un espace de noms avec une instruction **using** dans un langage de programmation, vous n’affectez pas les objets de l’espace de noms d’origine. Dans le langage CSDL, un espace de noms importé peut contenir un type d'entité dérivé d'un type d'entité figurant dans l'espace de noms d'origine. Cela peut affecter les jeux d'entités déclarés dans l'espace de noms d'origine.

 

L’élément **using** peut avoir les éléments enfants suivants :

-   Documentation (zéro ou un élément autorisé) ;
-   éléments d'annotation (zéro, un ou plusieurs éléments autorisés).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **using** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espace de noms**  | Oui         | Nom de l'espace de noms importé.                                                                                                                                                |
| **Alias**      | Oui         | Identificateur utilisé à la place du nom de l'espace de noms. Bien que cet attribut soit obligatoire, il n'est pas nécessaire qu'il soit utilisé à la place du nom de l'espace de noms pour qualifier les noms d'objets. |

 

> [!NOTE]
> Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **using** . Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL. Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.

 

### <a name="example"></a>Exemple

L’exemple suivant illustre l' **utilisation** de l’élément Using pour importer un espace de noms défini ailleurs. Notez que l’espace de noms de l’élément **Schema** indiqué est `BooksModel` . La `Address` propriété sur l' `Publisher` **EntityType** est un type complexe qui est défini dans l' `ExtendedBooksModel` espace de noms (importé avec l’élément **using** ).

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a>Attributs d'annotation (CSDL)

Les attributs d'annotation dans le langage CSDL (Conceptual Schema Definition Language) sont des attributs XML personnalisés dans le modèle conceptuel. En plus d'avoir une structure XML valide, les attributs d'annotation doivent satisfaire les critères suivants :

-   Les attributs d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage CSDL.
-   Plusieurs attributs d'annotation peuvent être appliqués à un élément CSDL donné.
-   Les noms qualifiés complets de deux attributs d'annotation ne doivent pas être identiques.

Les attributs d'annotation peuvent être utilisés pour fournir des métadonnées supplémentaires sur des éléments dans un modèle conceptuel. Vous pouvez accéder aux métadonnées contenues dans les éléments d’annotation au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec un attribut d’annotation (**CustomAttribute**). L'exemple fait également apparaître un élément d'annotation appliqué à l'élément de type d'entité.

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

Le code suivant récupère les métadonnées dans l'attribut d'annotation et les écrit dans la console :

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

Le code ci-dessus suppose que le fichier `School.csdl` se trouve dans le répertoire de sortie du projet et que vous avez ajouté les instructions `Imports` et `Using` suivantes à votre projet :

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Éléments d'annotation (CSDL)

Les éléments d'annotation dans le langage CSDL (Conceptual Schema Definition Language) sont des éléments XML personnalisés dans le modèle conceptuel. En plus d'avoir une structure XML valide, les éléments d'annotation doivent satisfaire les critères suivants :

-   Les éléments d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage CSDL.
-   Plusieurs éléments d'annotation peuvent être des enfants d'un élément CSDL donné.
-   Les noms qualifiés complets de deux éléments d'annotation ne doivent pas être identiques.
-   Les éléments d'annotation doivent apparaître après tous les autres éléments enfants d'un élément CSDL donné.

Les éléments d'annotation permettent de fournir des métadonnées supplémentaires sur les éléments dans un modèle conceptuel. À partir de la .NET Framework version 4, les métadonnées contenues dans les éléments d’annotation sont accessibles au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityType** avec un élément annotation (**customelement**). L'exemple fait également apparaître un attribut d'annotation appliqué à l'élément de type d'entité.

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

Le code suivant récupère les métadonnées dans l'élément d'annotation et les écrit dans la console :

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

Le code ci-dessus suppose que le fichier School.csdl se trouve dans le répertoire de sortie du projet et que vous avez ajouté les instructions `Imports` et `Using` suivantes à votre projet :

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>Types de modèles conceptuels (CSDL)

Le langage CSDL (Conceptual Schema Definition Language) prend en charge un ensemble de types de données primitifs abstraits, appelés **EDMSimpleTypes**, qui définissent des propriétés dans un modèle conceptuel. Les **EDMSimpleTypes** sont des proxys pour les types de données primitifs pris en charge dans l’environnement de stockage ou d’hébergement.

Le tableau suivant répertorie les types de données primitifs pris en charge par CSDL. Le tableau répertorie également les facettes qui peuvent être appliquées à chaque **type EDMSimpleType**.

| Type EDMSimpleType                    | Description                                                | Facettes applicables                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | Contient des données binaires.                                      | MaxLength, FixedLength, Nullable, Default                                |
| **Edm.Boolean**                  | Contient la valeur **true** ou **false**.                  | Nullable, Default                                                        |
| **Edm.Byte**                     | Contient une valeur d'entier 8 bits non signé.                  | Precision, Nullable, Default                                             |
| **Edm.DateTime**                 | Représente une date et une heure.                                | Precision, Nullable, Default                                             |
| **Edm.DateTimeOffset**           | Contient une date et une heure en tant que décalage en minutes par rapport à l'heure GMT. | Precision, Nullable, Default                                             |
| **Edm.Decimal**                  | Contient une valeur numérique avec une précision et une échelle fixes.   | Precision, Nullable, Default                                             |
| **Edm.Double**                   | Contient un nombre à virgule flottante avec une précision de 15 chiffres   | Precision, Nullable, Default                                             |
| **EDM. float**                    | Contient un nombre à virgule flottante avec une précision de 7 chiffres.   | Precision, Nullable, Default                                             |
| **Edm.Guid**                     | Contient un identificateur unique de 16 octets.                      | Precision, Nullable, Default                                             |
| **Edm.Int16**                    | Contient une valeur d'entier 16 bits signé.                    | Precision, Nullable, Default                                             |
| **Edm.Int32**                    | Contient une valeur d'entier 32 bits signé.                    | Precision, Nullable, Default                                             |
| **Edm.Int64**                    | Contient une valeur d'entier 64 bits signé.                    | Precision, Nullable, Default                                             |
| **Edm.SByte**                    | Contient une valeur d'entier 8 bits signé.                     | Precision, Nullable, Default                                             |
| **Edm.String**                   | Contient des données caractères.                                   | Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default |
| **Edm.Time**                     | Contient une heure.                                    | Precision, Nullable, Default                                             |
| **EDM. Geography**                |                                                            | Nullable, par défaut, SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyLineString**      |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyPolygon**         |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyMultiPoint**      |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyMultiLineString** |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyMultiPolygon**    |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeographyCollection**      |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. Geometry**                 |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryPoint**            |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryLineString**       |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryPolygon**          |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryMultiPoint**       |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryMultiLineString**  |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryMultiPolygon**     |                                                            | Nullable, par défaut, SRID                                                  |
| **EDM. GeometryCollection**       |                                                            | Nullable, par défaut, SRID                                                  |

## <a name="facets-csdl"></a>Facettes (CSDL)

Les facettes dans le langage CSDL (Conceptual Schema Definition Language) représentent des contraintes sur les propriétés de types d'entités et de types complexes. Les facettes apparaissent comme des attributs XML sur les éléments CSDL suivants :

-   Propriété
-   TypeRef
-   Paramètre

Le tableau ci-dessous décrit les facettes prises en charge dans le langage CSDL. Toutes les facettes sont facultatives. Certaines facettes répertoriées ci-dessous sont utilisées par la Entity Framework lors de la génération d’une base de données à partir d’un modèle conceptuel.

> [!NOTE]
> Pour plus d’informations sur les types de données dans un modèle conceptuel, consultez types de modèles conceptuels (CSDL).

| Facette               | Description                                                                                                                                                                                                                                                   | S’applique à                                                                                                                                                                                                                                                                                                                                                                           | Utilisée pour la génération de base de données. | Utilisée par le runtime. |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **Classement**       | Spécifie la table de classement ou ordre de tri à utiliser lors de l'exécution d'opérations de comparaison et de tri sur des valeurs de la propriété.                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Oui                              | Non                  |
| **ConcurrencyMode** | Indique que la valeur de la propriété doit être utilisée pour des contrôles d'accès concurrentiel optimiste.                                                                                                                                                                    | Toutes les propriétés **type EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Non                               | Oui                 |
| **Par défaut**         | Spécifie la valeur par défaut de la propriété si aucune valeur n'est fournie en cas d'instanciation.                                                                                                                                                                       | Toutes les propriétés **type EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Oui                              | Oui                 |
| **Multiple**     | Spécifie si la longueur de la valeur de propriété peut varier.                                                                                                                                                                                                  | **Edm. Binary**, **Edm. String**                                                                                                                                                                                                                                                                                                                                                       | Oui                              | Non                  |
| **MaxLength**       | Spécifie la longueur maximale de la valeur de propriété.                                                                                                                                                                                                           | **Edm. Binary**, **Edm. String**                                                                                                                                                                                                                                                                                                                                                       | Oui                              | Non                  |
| **Nullable**        | Spécifie si la propriété peut avoir une valeur **null** .                                                                                                                                                                                                     | Toutes les propriétés **type EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Oui                              | Oui                 |
| **Précision**       | Pour les propriétés de type **Decimal**, spécifie le nombre de chiffres qu’une valeur de propriété peut avoir. Pour les propriétés de type **Time**, **DateTime**et **DateTimeOffset**, spécifie le nombre de chiffres pour la partie fractionnaire des secondes de la valeur de la propriété. | **Edm. DateTime**, **Edm. DateTimeOffset**, **Edm. Decimal**, **Edm. Time**                                                                                                                                                                                                                                                                                                              | Oui                              | Non                  |
| **Mettre à l'échelle**           | Spécifie le nombre de chiffres à droite de la virgule décimale pour la valeur de propriété.                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | Oui                              | Non                  |
| **SRID**            | Spécifie l’ID du système de référence système spatial. Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).                                                              | **EDM. Geography, EDM. GeographyPoint, EDM. GeographyLineString, EDM. GeographyPolygon, EDM. GeographyMultiPoint, EDM. GeographyMultiLineString, EDM. GeographyMultiPolygon, EDM. GeographyCollection, EDM. Geometry, EDM. GeometryPoint, EDM. GeometryLineString, EDM. GeometryPolygon, EDM. GeometryMultiPoint, EDM. GeometryMultiLineString, EDM. GeometryMultiPolygon, EDM. GeometryCollection** | Non                               | Oui                 |
| **Unicode**         | Indique si la valeur de propriété est stockée au format Unicode.                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Oui                              | Oui                 |

>[!NOTE]
> Lors de la génération d’une base de données à partir d’un modèle conceptuel, l’Assistant génération de base de données reconnaît la valeur de l’attribut **StoreGeneratedPattern** sur un élément de **propriété** s’il se trouve dans l’espace de noms suivant : https://schemas.microsoft.com/ado/2009/02/edm/annotation . Les valeurs prises en charge pour l’attribut sont **Identity** et **computeed**. La valeur **Identity** produit une colonne de base de données avec une valeur d’identité générée dans la base de données. Une valeur **calculée** génère une colonne avec une valeur qui est calculée dans la base de données.

### <a name="example"></a>Exemple

L'exemple suivant illustre l'application de facettes aux propriétés d'un type d'entité :

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
