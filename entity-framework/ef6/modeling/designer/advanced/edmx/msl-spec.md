---
title: Spécification MSL-EF6
description: Spécification MSL dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 0780a549d4cbb60bf84bbed927d52588e99148e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066341"
---
# <a name="msl-specification"></a>Spécification MSL
Le langage MSL (Mapping Specification Language) est un langage basé sur XML qui décrit le mappage entre le modèle conceptuel et le modèle de stockage d’une application Entity Framework.

Dans une application Entity Framework, les métadonnées de mappage sont chargées à partir d’un fichier. MSL (écrit en MSL) au moment de la génération. Entity Framework utilise les métadonnées de mappage au moment de l’exécution pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques au stockage.

Le Entity Framework Designer (concepteur EF) stocke les informations de mappage dans un fichier. edmx au moment de la conception. Au moment de la génération, le Entity Designer utilise les informations d’un fichier. edmx pour créer le fichier. MSL requis par Entity Framework au moment de l’exécution.

Les noms de tous les types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif. Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez [spécification CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec). Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez la [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Les versions de MSL sont différenciées par les espaces de noms XML.

| Version MSL | Espace de noms XML                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn : schemas-microsoft-com : Windows : Storage : Mapping : CS |
| MSL v2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL v3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Élément Alias (MSL)

L’élément d' **alias** en Mapping Specification Language (MSL) est un enfant de l’élément de mappage utilisé pour définir des alias pour les espaces de noms du modèle conceptuel et du modèle de stockage. Les noms de tous les types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif. Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez Schema, élément (CSDL). Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez Schema, élément (SSDL).

L’élément **alias** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **alias** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Clé**        | Oui         | Alias de l’espace de noms spécifié par l’attribut **value** . |
| **Valeur**      | Oui         | Espace de noms pour lequel la valeur de l’élément **Key** est un alias.     |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément d' **alias** qui définit un alias, `c` , pour les types définis dans le modèle conceptuel.

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a>AssociationEnd, élément (MSL)

L’élément **AssociationEnd** en Mapping Specification Language (MSL) est utilisé lorsque les fonctions de modification d’un type d’entité dans le modèle conceptuel sont mappées aux procédures stockées dans la base de données sous-jacente. Si une procédure stockée de modification accepte un paramètre dont la valeur est conservée dans une propriété Association, l’élément **AssociationEnd** mappe la valeur de la propriété au paramètre. Pour plus d'informations, voir l'exemple ci-dessous.

Pour plus d’informations sur le mappage des fonctions de modification de types d’entité aux procédures stockées, consultez l’élément ModificationFunctionMapping (MSL) et procédure pas à pas : mappage d’une entité à des procédures stockées.

L’élément **AssociationEnd** peut avoir les éléments enfants suivants :

-   ScalarProperty

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **AssociationEnd** .

| Nom de l'attribut     | Est obligatoire | Valeur                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet ;** | Oui         | Nom de l'association mappée.                                                                                                                                 |
| **From**           | Oui         | Valeur de l’attribut **FromRole** de la propriété de navigation qui correspond à l’Association qui est mappée. Pour plus d’informations, consultez NavigationProperty, élément (CSDL). |
| **To**             | Oui         | Valeur de l’attribut **ToRole** de la propriété de navigation qui correspond à l’Association qui est mappée. Pour plus d’informations, consultez NavigationProperty, élément (CSDL).   |

### <a name="example"></a>Exemple

Considérons le type d'entité de modèle conceptuel suivant :

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

Considérons également la procédure stockée suivante :

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

Pour mapper la fonction de mise à jour de l' `Course` entité à cette procédure stockée, vous devez fournir une valeur au paramètre **DepartmentID** . La valeur pour `DepartmentID` ne correspond pas à une propriété sur le type d'entité ; elle est contenue dans une association indépendante dont le mappage est indiqué ici :

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

Le code suivant montre l’élément **AssociationEnd** utilisé pour mapper la propriété **DepartmentID** de l’Association de ** \_ \_ service de cours FK** à la procédure stockée **UpdateCourse** (à laquelle la fonction de mise à jour du type d’entité **course** est mappée) :

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a>AssociationSetMapping, élément (MSL)

L’élément **AssociationSetMapping** en Mapping Specification Language (MSL) définit le mappage entre une association dans le modèle conceptuel et les colonnes de table dans la base de données sous-jacente.

Les associations dans le modèle conceptuel sont des types dont les propriétés représentent des colonnes de clé primaire et de clé étrangère dans la base de données sous-jacente. L’élément **AssociationSetMapping** utilise deux éléments EndProperty pour définir les mappages entre les propriétés de type d’association et les colonnes de la base de données. Vous pouvez placer des conditions sur ces mappages avec l'élément Condition. Mappez les fonctions d'insertion, de mise à jour et de suppression pour les associer aux procédures stockées dans la base de données avec l'élément ModificationFunctionMapping. Définir des mappages en lecture seule entre des associations et des colonnes de table à l’aide d’une chaîne de Entity SQL dans un élément QueryView.

> [!NOTE]
> Si une contrainte référentielle est définie pour une association dans le modèle conceptuel, l’Association n’a pas besoin d’être mappée avec un élément **AssociationSetMapping** . Si un élément **AssociationSetMapping** est présent pour une association qui a une contrainte référentielle, les mappages définis dans l’élément **AssociationSetMapping** seront ignorés. Pour plus d’informations, consultez ReferentialConstraint, élément (CSDL).

L’élément **AssociationSetMapping** peut avoir les éléments enfants suivants

-   QueryView (zéro ou un élément)
-   EndProperty (zéro ou deux éléments)
-   Condition (zéro, un ou plusieurs éléments).
-   ModificationFunctionMapping (zéro ou un élément)

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **AssociationSetMapping** .

| Nom de l'attribut     | Est obligatoire | Valeur                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **Nom**           | Oui         | Nom de l'ensemble d'associations du modèle conceptuel mappé.                      |
| **TypeName**       | Non          | Nom qualifié par un espace de noms du type d'association du modèle conceptuel mappé. |
| **StoreEntitySet** | Non          | Nom de la table mappée.                                                 |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **AssociationSetMapping** dans lequel l’Association de ** \_ \_ service de cours FK** définie dans le modèle conceptuel est mappée à la table **course** de la base de données. Les mappages entre les propriétés de type d’association et les colonnes de table sont spécifiés dans les éléments **EndProperty** enfants.

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a>ComplexProperty, élément (MSL)

Un élément **ComplexProperty** en Mapping Specification Language (MSL) définit le mappage entre une propriété de type complexe sur un type d’entité de modèle conceptuel et des colonnes de table dans la base de données sous-jacente. Les mappages de colonnes de propriété sont spécifiés dans des éléments ScalarProperty enfants.

L’élément de propriété **complexType** peut avoir les éléments enfants suivants :

-   ScalarProperty (zéro, un ou plusieurs éléments) ;
-   **ComplexProperty** (zéro ou plus)
-   ComplextTypeMapping (zéro, un ou plusieurs éléments) ;
-   Condition (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ComplexProperty** :

| Nom de l'attribut | Est obligatoire | Valeur                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom de la propriété complexe d'un type d'entité dans le modèle conceptuel mappé. |
| **TypeName**   | Non          | Nom qualifié par un espace de noms du type de propriété de modèle conceptuel.                              |

### <a name="example"></a>Exemple

L'exemple suivant est basé sur le modèle School. Le type complexe suivant a été ajouté au modèle conceptuel :

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

Les propriétés **LastName** et **FirstName** du type d’entité **Person** ont été remplacées par une propriété complexe, **Name**:

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

Le MSL suivant montre l’élément **ComplexProperty** utilisé pour mapper la propriété **Name** aux colonnes de la base de données sous-jacente :

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a>ComplexTypeMapping, élément (MSL)

L’élément **ComplexTypeMapping,** en Mapping Specification Language (MSL) est un enfant de l’élément ResultMapping et définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée dans la base de données sous-jacente lorsque les conditions suivantes sont remplies :

-   L'importation de fonction retourne un type complexe conceptuel.
-   Les noms de colonne retournés par la procédure stockée ne correspondent pas exactement aux noms de propriété du type complexe.

Par défaut, le mappage entre les colonnes retournées par une procédure stockée et un type complexe est basé sur les noms de colonne et de propriété. Si les noms de colonne ne correspondent pas exactement aux noms de propriété, vous devez utiliser l’élément **ComplexTypeMapping,** pour définir le mappage. Pour obtenir un exemple du mappage par défaut, consultez élément FunctionImportMapping (MSL).

L’élément **ComplexTypeMapping,** peut avoir les éléments enfants suivants :

-   ScalarProperty (zéro, un ou plusieurs éléments) ;

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ComplexTypeMapping,** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | Oui         | Nom qualifié par un espace de noms du type complexe mappé. |

### <a name="example"></a>Exemple

Examinez la procédure stockée suivante :

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

De même, considérons le type complexe de modèle conceptuel suivant :

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

Afin de créer une importation de fonction qui retourne des instances du type complexe précédent, le mappage entre les colonnes retournées par la procédure stockée et le type d’entité doit être défini dans un élément **ComplexTypeMapping,** :

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a>Condition, élément (MSL)

L’élément **condition** en Mapping Specification Language (MSL) place des conditions sur les mappages entre le modèle conceptuel et la base de données sous-jacente. Le mappage défini dans un nœud XML est valide si toutes les conditions, comme spécifié dans les éléments de **condition** enfants, sont remplies. À défaut, le mappage n'est pas valide. Par exemple, si un élément MappingFragment contient un ou plusieurs éléments enfants **condition** , le mappage défini dans le nœud **MappingFragment** n’est valide que si toutes les conditions des éléments de **condition** enfants sont remplies.

Chaque condition peut s’appliquer à un **nom** (le nom d’une propriété d’entité de modèle conceptuel, spécifié par l’attribut **Name** ) ou à un **ColumnName** (le nom d’une colonne dans la base de données, spécifié par l’attribut **ColumnName** ). Lorsque l’attribut **Name** est défini, la condition est vérifiée par rapport à une valeur de propriété d’entité. Lorsque l’attribut **ColumnName** est défini, la condition est vérifiée par rapport à une valeur de colonne. Seul l’un des attributs **Name** ou **ColumnName** peut être spécifié dans un élément **condition** .

> [!NOTE]
> Lorsque l’élément **condition** est utilisé dans un élément FunctionImportMapping, seul l’attribut **Name** n’est pas applicable.

L’élément **condition** peut être un enfant des éléments suivants :

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

L’élément **condition** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **condition** :

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | Non          | Nom de la colonne de table dont la valeur est utilisée pour évaluer la condition.                                                                                                                                                                                                                   |
| **IsNull**     | Non          | **True** ou **False**. Si la valeur est **true** et que la valeur de la colonne est **null**, ou si la valeur est **false** et que la valeur de la colonne n’est pas **null**, la condition est true. Sinon, la condition n'est pas vérifiée (False). <br/> Les attributs **IsNull** et **value** ne peuvent pas être utilisés en même temps. |
| **Valeur**      | Non          | Valeur à laquelle la valeur de colonne est comparée. Si les valeurs sont identiques, la condition est vérifiée (True). Sinon, la condition n'est pas vérifiée (False). <br/> Les attributs **IsNull** et **value** ne peuvent pas être utilisés en même temps.                                                                       |
| **Nom**       | Non          | Nom de la propriété d'entité de modèle conceptuel dont la valeur est utilisée pour évaluer la condition. <br/> Cet attribut n’est pas applicable si l’élément **condition** est utilisé dans un élément FunctionImportMapping.                                                                           |

### <a name="example"></a>Exemple

L’exemple suivant montre des éléments de **condition** en tant qu’enfants d’éléments **MappingFragment** . Lorsque **HireDate** n’a pas la valeur null et que **EnrollmentDate** a la valeur null, les données sont mappées entre le type **SchoolModel. Instructor** et les colonnes **PersonID** et **HireDate** de la table **Person** . Lorsque **EnrollmentDate** n’a pas la valeur null et **HireDate** a la valeur null, les données sont mappées entre le type **SchoolModel. Student** et les colonnes **PersonID** et **inscription** de la table **Person** .

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a>DeleteFunction, élément (MSL)

L’élément **DeleteFunction** en Mapping Specification Language (MSL) mappe la fonction Delete d’un type d’entité ou d’une association dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente. Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage. Pour plus d’informations, consultez Function, élément (SSDL).

> [!NOTE]
> Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.

### <a name="deletefunction-applied-to-entitytypemapping"></a>Application de DeleteFunction à EntityTypeMapping

Lorsqu’il est appliqué à l’élément EntityTypeMapping, l’élément **DeleteFunction** mappe la fonction Delete d’un type d’entité dans le modèle conceptuel à une procédure stockée.

L’élément **DeleteFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à un élément **EntityTypeMapping** :

-   AssociationEnd (zéro, un ou plusieurs éléments) ;
-   ComplexProperty (zéro, un ou plusieurs éléments) ;
-   ScalarProperty (zéro, un ou plusieurs éléments).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **DeleteFunction** lorsqu’il est appliqué à un élément **EntityTypeMapping** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Oui         | Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de suppression est mappée. La procédure stockée doit être déclarée dans le modèle de stockage. |
| **RowsAffectedParameter** | Non          | Nom du paramètre de sortie qui retourne le nombre de lignes affectées.                                                                               |

#### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre l’élément **DeleteFunction** qui mappe la fonction Delete du type d’entité **Person** à la procédure stockée **DeletePerson** . La procédure stockée **DeletePerson** est déclarée dans le modèle de stockage.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a>Application de DeleteFunction à AssociationSetMapping

Lorsqu’il est appliqué à l’élément AssociationSetMapping, l’élément **DeleteFunction** mappe la fonction Delete d’une association dans le modèle conceptuel à une procédure stockée.

L’élément **DeleteFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à l’élément **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **DeleteFunction** lorsqu’il est appliqué à l’élément **AssociationSetMapping** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Oui         | Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de suppression est mappée. La procédure stockée doit être déclarée dans le modèle de stockage. |
| **RowsAffectedParameter** | Non          | Nom du paramètre de sortie qui retourne le nombre de lignes affectées.                                                                               |

#### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre l’élément **DeleteFunction** utilisé pour mapper la fonction Delete de l’Association **CourseInstructor** à la procédure stockée **DeleteCourseInstructor** . La procédure stockée **DeleteCourseInstructor** est déclarée dans le modèle de stockage.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a>EndProperty, élément (MSL)

L’élément **EndProperty** en Mapping Specification Language (MSL) définit le mappage entre une terminaison ou une fonction de modification d’une association de modèle conceptuel et la base de données sous-jacente. Le mappage de colonne de propriété est spécifié dans un élément ScalarProperty enfant.

Lorsqu’un élément **EndProperty** est utilisé pour définir le mappage de la fin d’une association de modèle conceptuel, il est un enfant d’un élément AssociationSetMapping. Lorsque l’élément **EndProperty** est utilisé pour définir le mappage d’une fonction de modification d’une association de modèle conceptuel, il est un enfant d’un élément InsertFunction ou d’un élément DeleteFunction.

L’élément **EndProperty** peut avoir les éléments enfants suivants :

-   ScalarProperty (zéro, un ou plusieurs éléments) ;

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **EndProperty** :

| Nom de l'attribut | Est obligatoire | Valeur                                                 |
|:---------------|:------------|:------------------------------------------------------|
| Nom           | Oui         | Nom de la terminaison d'association mappée. |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **AssociationSetMapping** dans lequel l’Association de ** \_ \_ service de cours FK** dans le modèle conceptuel est mappée à la table **course** de la base de données. Les mappages entre les propriétés de type d’association et les colonnes de table sont spécifiés dans les éléments **EndProperty** enfants.

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **EndProperty** qui mappe les fonctions d’insertion et de suppression d’une association (**CourseInstructor**) à des procédures stockées dans la base de données sous-jacente. Les fonctions mappées sont déclarées dans le modèle de stockage.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a>EntityContainerMapping, élément (MSL)

L’élément **EntityContainerMapping** en Mapping Specification Language (MSL) mappe le conteneur d’entités du modèle conceptuel au conteneur d’entités dans le modèle de stockage. L’élément **EntityContainerMapping** est un enfant de l’élément Mapping.

L’élément **EntityContainerMapping** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :

-   EntitySetMapping (zéro, un ou plusieurs éléments) ;
-   AssociationSetMapping (zéro, un ou plusieurs éléments) ;
-   FunctionImportMapping (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntityContainerMapping** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | Oui         | Nom du conteneur d'entités de modèle de stockage mappé.                                                                                                                                                                                     |
| **CdmEntityContainer**    | Oui         | Nom du conteneur d'entités de modèle conceptuel mappé.                                                                                                                                                                                  |
| **GenerateUpdateViews**   | Non          | **True** ou **False**. Si la **valeur est false**, aucune vue de mise à jour n’est générée. Cet attribut doit avoir la valeur **false** lorsque vous avez un mappage en lecture seule qui serait non valide, car les données ne peuvent pas aller-retour. <br/> La valeur par défaut est **True**. |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntityContainerMapping** qui mappe le conteneur **SchoolModelEntities** (le conteneur d’entités de modèle conceptuel) au conteneur **SchoolModelStoreContainer** (conteneur d’entités de modèle de stockage) :

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a>EntitySetMapping, élément (MSL)

L’élément **EntitySetMapping** en Mapping Specification Language (MSL) mappe tous les types dans un jeu d’entités de modèle conceptuel aux jeux d’entités dans le modèle de stockage. Un jeu d'entités dans le modèle conceptuel est un conteneur logique pour instances d'entités de même type (et des types dérivés). Un jeu d'entités dans le modèle de stockage représente une table ou une vue de la base de données sous-jacente. Le jeu d’entités du modèle conceptuel est spécifié par la valeur de l’attribut **Name** de l’élément **EntitySetMapping** . La table ou la vue mappée à est spécifiée par l’attribut **StoreEntitySet** dans chaque élément MappingFragment enfant ou dans l’élément **EntitySetMapping** lui-même.

L’élément **EntitySetMapping** peut avoir les éléments enfants suivants :

-   EntityTypeMapping (zéro, un ou plusieurs éléments) ;
-   QueryView (zéro ou un élément)
-   MappingFragment (zéro, un ou plusieurs éléments) ;

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntitySetMapping** .

| Nom de l'attribut           | Est obligatoire | Valeur                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**                 | Oui         | Nom du jeu d'entités de modèle conceptuel mappé.                                                                                                                                                             |
| **TypeName** **1**       | Non          | Nom du type d'entité de modèle conceptuel mappé.                                                                                                                                                            |
| **StoreEntitySet** **1** | Non          | Nom du jeu d'entités de modèle de stockage de destination du mappage.                                                                                                                                                             |
| **MakeColumnsDistinct**  | Non          | **True** ou **false** selon que seules des lignes distinctes sont retournées. <br/> Si cet attribut est défini sur **true**, l’attribut **GenerateUpdateViews** de l’élément EntityContainerMapping doit avoir la valeur **false**. |

 

**1** les attributs **TypeName** et **StoreEntitySet** peuvent être utilisés à la place des éléments enfants EntityTypeMapping et MappingFragment pour mapper un type d’entité unique à une table unique.

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **EntitySetMapping** qui mappe trois types (un type de base et deux types dérivés) dans le jeu d’entités **courses** du modèle conceptuel à trois tables différentes dans la base de données sous-jacente. Les tables sont spécifiées par l’attribut **StoreEntitySet** dans chaque élément **MappingFragment** .

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a>EntityTypeMapping, élément (MSL)

L’élément **EntityTypeMapping** en Mapping Specification Language (MSL) définit le mappage entre un type d’entité dans le modèle conceptuel et les tables ou vues dans la base de données sous-jacente. Pour plus d'informations sur les types d'entité de modèle conceptuel et les tables ou les vues de base de données sous-jacente, consultez Élément EntityType (CSDL) et Élément EntitySet (SSDL). Le type d’entité de modèle conceptuel qui est mappé est spécifié par l’attribut **TypeName** de l’élément **EntityTypeMapping** . La table ou la vue mappée est spécifiée par l’attribut **StoreEntitySet** de l’élément MappingFragment enfant.

L'élément enfant ModificationFunctionMapping peut être utilisé pour mapper les fonctions d'insertion, de mise à jour ou de suppression de types d'entités aux procédures stockées de la base de données.

L’élément **EntityTypeMapping** peut avoir les éléments enfants suivants :

-   MappingFragment (zéro, un ou plusieurs éléments) ;
-   ModificationFunctionMapping (zéro ou un élément)
-   ScalarProperty
-   Condition

> [!NOTE]
> Les éléments **MappingFragment** et **ModificationFunctionMapping** ne peuvent pas être des éléments enfants de l’élément **EntityTypeMapping** en même temps.


> [!NOTE]
> Les éléments **ScalarProperty** et **condition** ne peuvent être que des éléments enfants de l’élément **EntityTypeMapping** lorsqu’il est utilisé dans un élément FunctionImportMapping.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntityTypeMapping** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | Oui         | Nom qualifié par un espace de noms du type d'entité de modèle conceptuel mappé. <br/> Si le type correspond à un type abstrait ou dérivé, la valeur doit être `IsOfType(Namespace-qualified_type_name)`. |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément EntitySetMapping avec deux éléments **EntityTypeMapping** enfants. Dans le premier élément **EntityTypeMapping** , le type d’entité **SchoolModel. Person** est mappé à la table **Person** . Dans le deuxième élément **EntityTypeMapping** , la fonctionnalité de mise à jour du type **SchoolModel. Person** est mappée à une procédure stockée, **UpdatePerson**, dans la base de données.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Exemple

L'exemple suivant illustre le mappage d'une hiérarchie de types dont le type racine est abstrait. Notez l’utilisation de la `IsOfType` syntaxe pour les attributs **TypeName** .

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a>FunctionImportMapping, élément (MSL)

L’élément **FunctionImportMapping** en Mapping Specification Language (MSL) définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée ou une fonction dans la base de données sous-jacente. Les importations de fonction doivent être déclarées dans le modèle conceptuel et les procédures stockées dans le modèle de stockage. Pour plus d’informations, consultez FunctionImport, élément (CSDL) et Function, élément (SSDL).

> [!NOTE]
> Par défaut, si une importation de fonction retourne un type d'entité ou un type complexe de modèle conceptuel, les noms des colonnes retournés par la procédure stockée sous-jacente doivent correspondre exactement aux noms des propriétés sur le type de modèle conceptuel. Si les noms de colonne ne correspondent pas exactement aux noms de propriété, le mappage doit être défini dans un élément ResultMapping.

L’élément **FunctionImportMapping** peut avoir les éléments enfants suivants :

-   ResultMapping (zéro ou plus)

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **FunctionImportMapping** :

| Nom de l'attribut         | Est obligatoire | Valeur                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | Oui         | Nom de l'importation de fonction dans le modèle conceptuel mappé.           |
| **FunctionName**       | Oui         | Nom qualifié par un espace de noms de la fonction dans le modèle de stockage mappé. |

### <a name="example"></a>Exemple

L'exemple suivant est basé sur le modèle School. Considérez la fonction suivante dans le modèle de stockage :

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

Considérez également cette importation de fonction dans le modèle conceptuel :

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

L’exemple suivant montre un élément **FunctionImportMapping** utilisé pour mapper la fonction et l’importation de fonction ci-dessus :

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction, élément (MSL)

L’élément **InsertFunction** en Mapping Specification Language (MSL) mappe la fonction d’insertion d’un type d’entité ou d’une association dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente. Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage. Pour plus d’informations, consultez Function, élément (SSDL).

> [!NOTE]
> Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.

L’élément **InsertFunction** peut être un enfant de l’élément ModificationFunctionMapping et être appliqué à l’élément EntityTypeMapping ou à l’élément AssociationSetMapping.

### <a name="insertfunction-applied-to-entitytypemapping"></a>Application d'InsertFunction à EntityTypeMapping

Lorsqu’il est appliqué à l’élément EntityTypeMapping, l’élément **InsertFunction** mappe la fonction d’insertion d’un type d’entité dans le modèle conceptuel à une procédure stockée.

L’élément **InsertFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à un élément **EntityTypeMapping** :

-   AssociationEnd (zéro, un ou plusieurs éléments) ;
-   ComplexProperty (zéro, un ou plusieurs éléments) ;
-   ResultBinding (zéro ou un élément) ;
-   ScalarProperty (zéro, un ou plusieurs éléments).

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **InsertFunction** lorsqu’ils sont appliqués à un élément **EntityTypeMapping** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Oui         | Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction d'insertion est mappée. La procédure stockée doit être déclarée dans le modèle de stockage. |
| **RowsAffectedParameter** | Non          | Nom du paramètre de sortie qui retourne le nombre de lignes affectées.                                                                               |

#### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre l’élément **InsertFunction** utilisé pour mapper la fonction d’insertion du type d’entité Person à la procédure stockée **InsertPerson** . La procédure stockée **InsertPerson** est déclarée dans le modèle de stockage.

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a>Application d'InsertFunction à AssociationSetMapping

Lorsqu’il est appliqué à l’élément AssociationSetMapping, l’élément **InsertFunction** mappe la fonction d’insertion d’une association dans le modèle conceptuel à une procédure stockée.

L’élément **InsertFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à l’élément **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **InsertFunction** lorsqu’il est appliqué à l’élément **AssociationSetMapping** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Oui         | Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction d'insertion est mappée. La procédure stockée doit être déclarée dans le modèle de stockage. |
| **RowsAffectedParameter** | Non          | Nom du paramètre de sortie qui retourne le nombre de lignes affectées.                                                                               |

#### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre l’élément **InsertFunction** utilisé pour mapper la fonction d’insertion de l’Association **CourseInstructor** à la procédure stockée **InsertCourseInstructor** . La procédure stockée **InsertCourseInstructor** est déclarée dans le modèle de stockage.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a>Élément de mappage (MSL)

L’élément **Mapping** en Mapping Specification Language (MSL) contient des informations pour le mappage d’objets définis dans un modèle conceptuel à une base de données (comme décrit dans un modèle de stockage). Pour plus d’informations, consultez Spécification CSDL et spécification SSDL.

L’élément **Mapping** est l’élément racine d’une spécification de mappage. L’espace de noms XML pour les spécifications de mappage est https://schemas.microsoft.com/ado/2009/11/mapping/cs .

L'élément de mappage peut avoir les éléments enfants suivants (dans l'ordre répertorié) :

-   Alias (zéro, un ou plusieurs éléments) ;
-   EntityContainerMapping (exactement un élément).

Les noms de types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif. Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez Schema, élément (CSDL). Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez Schema, élément (SSDL). Les alias d'espace de noms utilisés en MSL peuvent être définis avec l'élément Alias.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Mapping** .

| Nom de l'attribut | Est obligatoire | Valeur                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **Espace**      | Oui         | **C-S**. Il s'agit d'une valeur fixe qui ne peut pas être modifiée. |

### <a name="example"></a>Exemple

L’exemple suivant illustre un élément de **mappage** basé sur une partie du modèle School. Pour plus d’informations sur le modèle School, consultez démarrage rapide (Entity Framework) :

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a>MappingFragment, élément (MSL)

L’élément **MappingFragment** en Mapping Specification Language (MSL) définit le mappage entre les propriétés d’un type d’entité de modèle conceptuel et une table ou une vue dans la base de données. Pour plus d'informations sur les types d'entité de modèle conceptuel et les tables ou les vues de base de données sous-jacente, consultez Élément EntityType (CSDL) et Élément EntitySet (SSDL). L’élément **MappingFragment** peut être un élément enfant de l’élément EntityTypeMapping ou de l’élément EntitySetMapping.

L’élément **MappingFragment** peut avoir les éléments enfants suivants :

-   ComplexType (zéro, un ou plusieurs éléments) ;
-   ScalarProperty (zéro, un ou plusieurs éléments) ;
-   Condition (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **MappingFragment** .

| Nom de l'attribut          | Est obligatoire | Valeur                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | Oui         | Nom de la table ou de la vue mappée.                                                                                                                                                                           |
| **MakeColumnsDistinct** | Non          | **True** ou **false** selon que seules des lignes distinctes sont retournées. <br/> Si cet attribut est défini sur **true**, l’attribut **GenerateUpdateViews** de l’élément EntityContainerMapping doit avoir la valeur **false**. |

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **MappingFragment** en tant qu’enfant d’un élément **EntityTypeMapping** . Dans cet exemple, les propriétés du type de **cours** dans le modèle conceptuel sont mappées aux colonnes de la table **course** dans la base de données.

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Exemple

L’exemple suivant montre un élément **MappingFragment** en tant qu’enfant d’un élément **EntitySetMapping** . Comme dans l’exemple ci-dessus, les propriétés du type de **cours** dans le modèle conceptuel sont mappées aux colonnes de la table **course** dans la base de données.

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a>ModificationFunctionMapping, élément (MSL)

L’élément **ModificationFunctionMapping** en Mapping Specification Language (MSL) mappe les fonctions d’insertion, de mise à jour et de suppression d’un type d’entité de modèle conceptuel aux procédures stockées dans la base de données sous-jacente. L’élément **ModificationFunctionMapping** peut également mapper les fonctions d’insertion et de suppression pour les associations plusieurs-à-plusieurs dans le modèle conceptuel aux procédures stockées dans la base de données sous-jacente. Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage. Pour plus d’informations, consultez Function, élément (SSDL).

> [!NOTE]
> Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.


> [!NOTE]
> Si les fonctions de modification pour une entité dans une hiérarchie d'héritage sont mappées aux procédures stockées, les fonctions de modification de tous les types dans la hiérarchie doivent être mappées aux procédures stockées.

L’élément **ModificationFunctionMapping** peut être un enfant de l’élément EntityTypeMapping ou de l’élément AssociationSetMapping.

L’élément **ModificationFunctionMapping** peut avoir les éléments enfants suivants :

-   DeleteFunction (zéro ou un élément) ;
-   InsertFunction (zéro ou un élément) ;
-   UpdateFunction (zéro ou un élément).

Aucun attribut n’est applicable à l’élément **ModificationFunctionMapping** .

### <a name="example"></a>Exemple

L’exemple suivant montre le mappage de jeu d’entités pour le jeu d’entités **People** dans le modèle School. En plus du mappage de colonnes pour le type d’entité **Person** , le mappage des fonctions d’insertion, de mise à jour et de suppression du type **Person** est affiché. Les fonctions mappées sont déclarées dans le modèle de stockage.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Exemple

L’exemple suivant montre le mappage de l’ensemble d’associations pour l’ensemble d’associations **CourseInstructor** dans le modèle School. En plus du mappage de colonnes pour l’Association **CourseInstructor** , le mappage des fonctions d’insertion et de suppression de l’Association **CourseInstructor** est affiché. Les fonctions mappées sont déclarées dans le modèle de stockage.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a>QueryView, élément (MSL)

L’élément **QueryView** en Mapping Specification Language (MSL) définit un mappage en lecture seule entre un type d’entité ou une association dans le modèle conceptuel et une table dans la base de données sous-jacente. Le mappage est défini avec une requête Entity SQL qui est évaluée par rapport au modèle de stockage, et vous exprimez le jeu de résultats en termes d’entité ou d’association dans le modèle conceptuel. Les affichages des requêtes étant en lecture seule, les types qu'ils définissent ne peuvent pas être mis à jour au moyen des commandes de mise à jour standard. Les mises à jour de ces types peuvent être effectuées au moyen de fonctions de modification. Pour plus d’informations, consultez Comment : mapper des fonctions de modification à des procédures stockées.

> [!NOTE]
> Dans l’élément **QueryView** , Entity SQL expressions qui contiennent des propriétés **GroupBy**, Group ou de navigation ne sont pas prises en charge.

 

L’élément **QueryView** peut être un enfant de l’élément EntitySetMapping ou de l’élément AssociationSetMapping. Dans le cas précédent, l'affichage des requêtes définit un mappage en lecture seule pour une entité dans le modèle conceptuel. Dans le cas précédent, l'affichage des requêtes définit un mappage en lecture seule pour une association dans le modèle conceptuel.

> [!NOTE]
> Si l’élément **AssociationSetMapping** est destiné à une association avec une contrainte référentielle, l’élément **AssociationSetMapping** est ignoré. Pour plus d’informations, consultez ReferentialConstraint, élément (CSDL).

L’élément **QueryView** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **QueryView** .

| Nom de l'attribut | Est obligatoire | Valeur                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | Non          | Nom du type de modèle conceptuel mappé par l'affichage des requêtes. |

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **QueryView** en tant qu’enfant de l’élément **EntitySetMapping** et définit un mappage d’affichage des requêtes pour le type d’entité **Department** dans le modèle School.

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

Étant donné que la requête retourne uniquement un sous-ensemble des membres du type de **service** dans le modèle de stockage, le type de **service** du modèle School a été modifié en fonction de ce mappage comme suit :

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **QueryView** en tant qu’enfant d’un élément **AssociationSetMapping** et définit un mappage en lecture seule pour l' `FK_Course_Department` Association dans le modèle School.

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a>Commentaires

Vous pouvez définir des affichages des requêtes pour activer les scénarios suivants :

-   Définir une entité du modèle conceptuel qui n'inclut pas toutes les propriétés de l'entité dans le modèle de stockage. Cela comprend les propriétés qui n’ont pas de valeurs par défaut et qui ne prennent pas en charge les valeurs **null** .
-   Mapper des colonnes calculées du modèle de stockage aux propriétés de types d'entités du modèle conceptuel.
-   Définir un mappage dans lequel les conditions utilisées pour partitionner des entités du modèle conceptuel ne sont pas basées sur l'égalité. Lorsque vous spécifiez un mappage conditionnel à l’aide de l’élément **condition** , la condition fournie doit être égale à la valeur spécifiée. Pour plus d’informations, consultez élément condition (MSL).
-   Mapper la même colonne du modèle de stockage à plusieurs types du modèle conceptuel.
-   Mapper plusieurs types à la même table.
-   Définir des associations dans le modèle conceptuel qui ne sont pas basées sur des clés étrangères du schéma relationnel.
-   Utiliser une logique métier personnalisée pour définir la valeur de propriétés du modèle conceptuel. Par exemple, vous pouvez mapper la valeur de chaîne « T » dans la source de données à la valeur **true**, une valeur booléenne, dans le modèle conceptuel.
-   Définir des filtres conditionnels pour les résultats de la requête.
-   Appliquer moins de restrictions sur les données dans le modèle conceptuel que dans le modèle de stockage. Par exemple, vous pouvez rendre une propriété dans le modèle conceptuel Nullable même si la colonne à laquelle elle est mappée ne prend pas en charge les valeurs **null**.

Vous devez tenir compte des points suivants lorsque vous définissez des affichages des requêtes pour les entités :

-   Les affichages des requêtes sont en lecture seule. Les mises à jour des entités ne peuvent être effectuées qu'au moyen de fonctions de modification.
-   Lorsque vous définissez un type d'entité par un affichage des requêtes, vous devez également définir toutes les entités associées par les affichages des requêtes.
-   Lorsque vous mappez une association plusieurs-à-plusieurs à une entité dans le modèle de stockage qui représente une table de liens dans le schéma relationnel, vous devez définir un élément **QueryView** dans l’élément **AssociationSetMapping** pour cette table de liens.
-   Les affichages des requêtes doivent être définis pour tous les types d'une hiérarchie des types. Pour ce faire, vous pouvez procéder de différentes façons :
-   -   Avec un seul élément **QueryView** qui spécifie une seule requête Entity SQL qui retourne une Union de tous les types d’entité dans la hiérarchie.
    -   Avec un seul élément **QueryView** qui spécifie une requête Entity SQL unique qui utilise l’opérateur case pour retourner un type d’entité spécifique dans la hiérarchie en fonction d’une condition spécifique.
    -   Avec un élément **QueryView** supplémentaire pour un type spécifique dans la hiérarchie. Dans ce cas, utilisez l’attribut **TypeName** de l’élément **QueryView** pour spécifier le type d’entité pour chaque vue.
-   Quand une vue de requête est définie, vous ne pouvez pas spécifier l’attribut **StorageSetName** sur l’élément **EntitySetMapping** .
-   Quand une vue de requête est définie, l’élément **EntitySetMapping**ne peut pas contenir également des mappages de **Propriétés** .

## <a name="resultbinding-element-msl"></a>Élément ResultBinding (MSL)

L’élément **ResultBinding** en Mapping Specification Language (MSL) mappe les valeurs de colonne retournées par les procédures stockées aux propriétés d’entité dans le modèle conceptuel lorsque les fonctions de modification de type d’entité sont mappées aux procédures stockées dans la base de données sous-jacente. Par exemple, lorsque la valeur d’une colonne d’identité est retournée par une procédure stockée Insert, l’élément **ResultBinding** mappe la valeur retournée à une propriété de type d’entité dans le modèle conceptuel.

L’élément **ResultBinding** peut être un enfant de l’élément InsertFunction ou de l’élément UpdateFunction.

L’élément **ResultBinding** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ResultBinding** :

| Nom de l'attribut | Est obligatoire | Valeur                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **Nom**       | Oui         | Nom de la propriété d'entité dans le modèle conceptuel mappé. |
| **ColumnName** | Oui         | Nom de la colonne mappée.                                          |

### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre un élément **InsertFunction** utilisé pour mapper la fonction insert du type d’entité **Person** à la procédure stockée **InsertPerson** . (La procédure stockée **InsertPerson** est indiquée ci-dessous et est déclarée dans le modèle de stockage.) Un élément **ResultBinding** est utilisé pour mapper une valeur de colonne retournée par la procédure stockée (**NewPersonID**) à une propriété de type d’entité (**PersonID**).

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

Le code Transact-SQL suivant décrit la procédure stockée **InsertPerson** :

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a>Élément ResultMapping (MSL)

L’élément **ResultMapping** en Mapping Specification Language (MSL) définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée dans la base de données sous-jacente lorsque les conditions suivantes sont remplies :

-   L'importation de fonction retourne un type d'entité de modèle conceptuel ou le type complexe.
-   Les noms des colonnes retournés par la procédure stockée ne correspondent pas exactement aux noms des propriétés sur le type d'entité ou le type complexe.

Par défaut, le mappage entre les colonnes retournées par une procédure stockée et un type d'entité ou un type complexe est basé sur les noms de colonne et de propriété. Si les noms de colonne ne correspondent pas exactement aux noms de propriété, vous devez utiliser l’élément **ResultMapping** pour définir le mappage. Pour obtenir un exemple du mappage par défaut, consultez élément FunctionImportMapping (MSL).

L’élément **ResultMapping** est un élément enfant de l’élément FunctionImportMapping.

L’élément **ResultMapping** peut avoir les éléments enfants suivants :

-   EntityTypeMapping (zéro, un ou plusieurs éléments) ;
-   ComplexTypeMapping

Aucun attribut n’est applicable à l’élément **ResultMapping** .

### <a name="example"></a>Exemple

Examinez la procédure stockée suivante :

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

De même, considérons le type d'entité de modèle conceptuel suivant :

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

Afin de créer une importation de fonction qui retourne des instances du type d’entité précédent, le mappage entre les colonnes retournées par la procédure stockée et le type d’entité doit être défini dans un élément **ResultMapping** :

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a>ScalarProperty, élément (MSL)

L’élément **ScalarProperty** en Mapping Specification Language (MSL) mappe une propriété sur un type d’entité de modèle conceptuel, un type complexe ou une association à une colonne de table ou un paramètre de procédure stockée dans la base de données sous-jacente.

> [!NOTE]
> Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage. Pour plus d’informations, consultez Function, élément (SSDL).

L’élément **ScalarProperty** peut être un enfant des éléments suivants :

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

En tant qu’enfant de l’élément **MappingFragment**, **ComplexProperty**ou **EndProperty** , l’élément **ScalarProperty** mappe une propriété du modèle conceptuel à une colonne de la base de données. En tant qu’enfant de l’élément **InsertFunction**, **UpdateFunction**ou **DeleteFunction** , l’élément **ScalarProperty** mappe une propriété du modèle conceptuel à un paramètre de procédure stockée.

L’élément **ScalarProperty** ne peut pas avoir d’éléments enfants.

### <a name="applicable-attributes"></a>Attributs applicables

Les attributs qui s’appliquent à l’élément **ScalarProperty** varient en fonction du rôle de l’élément.

Le tableau suivant décrit les attributs qui s’appliquent lorsque l’élément **ScalarProperty** est utilisé pour mapper une propriété de modèle conceptuel à une colonne de la base de données :

| Nom de l'attribut | Est obligatoire | Valeur                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nom**       | Oui         | Nom de la propriété de modèle conceptuel mappée. |
| **ColumnName** | Oui         | Nom de la colonne de table mappée.              |

Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ScalarProperty** lorsqu’il est utilisé pour mapper une propriété de modèle conceptuel à un paramètre de procédure stockée :

| Nom de l'attribut    | Est obligatoire | Valeur                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nom**          | Oui         | Nom de la propriété de modèle conceptuel mappée.                                                                                 |
| **ParameterName** | Oui         | Nom du paramètre mappé.                                                                                                 |
| **Version**       | Non          | **Actuel** ou **original** selon que la valeur actuelle ou la valeur d’origine de la propriété doit être utilisée pour les contrôles d’accès concurrentiel. |

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **ScalarProperty** utilisé de deux manières :

-   Pour mapper les propriétés du type d’entité **Person** aux colonnes de la table **Person**.
-   Pour mapper les propriétés du type d’entité **Person** aux paramètres de la procédure stockée **UpdatePerson** . Les procédures stockées sont déclarées dans le modèle de stockage.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Exemple

L’exemple suivant montre l’élément **ScalarProperty** utilisé pour mapper les fonctions d’insertion et de suppression d’une association de modèle conceptuel à des procédures stockées dans la base de données. Les procédures stockées sont déclarées dans le modèle de stockage.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a>UpdateFunction, élément (MSL)

L’élément **UpdateFunction** en Mapping Specification Language (MSL) mappe la fonction de mise à jour d’un type d’entité dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente. Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage. Pour plus d’informations, consultez Function, élément (SSDL).

> [!NOTE]
>  Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.

L’élément **UpdateFunction** peut être un enfant de l’élément ModificationFunctionMapping et être appliqué à l’élément EntityTypeMapping.

L’élément **UpdateFunction** peut avoir les éléments enfants suivants :

-   AssociationEnd (zéro, un ou plusieurs éléments) ;
-   ComplexProperty (zéro, un ou plusieurs éléments) ;
-   ResultBinding (zéro ou un élément) ;
-   ScalarProperty (zéro, un ou plusieurs éléments).

### <a name="applicable-attributes"></a>Attributs applicables

Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **UpdateFunction** .

| Nom de l'attribut            | Est obligatoire | Valeur                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Oui         | Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de mise à jour est mappée. La procédure stockée doit être déclarée dans le modèle de stockage. |
| **RowsAffectedParameter** | Non          | Nom du paramètre de sortie qui retourne le nombre de lignes affectées.                                                                               |

### <a name="example"></a>Exemple

L’exemple suivant est basé sur le modèle School et montre l’élément **UpdateFunction** utilisé pour mapper la fonction de mise à jour du type d’entité **Person** à la procédure stockée **UpdatePerson** . La procédure stockée **UpdatePerson** est déclarée dans le modèle de stockage.

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
