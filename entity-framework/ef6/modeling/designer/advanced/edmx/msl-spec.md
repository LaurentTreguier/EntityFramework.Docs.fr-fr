---
title: Spécification MSL-EF6
description: Spécification MSL dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 4ec43f48f82b83bd85b47fe234be143d0e4d2560
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620618"
---
# <a name="msl-specification"></a><span data-ttu-id="2d13d-103">Spécification MSL</span><span class="sxs-lookup"><span data-stu-id="2d13d-103">MSL Specification</span></span>
<span data-ttu-id="2d13d-104">Le langage MSL (Mapping Specification Language) est un langage basé sur XML qui décrit le mappage entre le modèle conceptuel et le modèle de stockage d’une application Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2d13d-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="2d13d-105">Dans une application Entity Framework, les métadonnées de mappage sont chargées à partir d’un fichier. MSL (écrit en MSL) au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="2d13d-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="2d13d-106">Entity Framework utilise les métadonnées de mappage au moment de l’exécution pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques au stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="2d13d-107">Le Entity Framework Designer (concepteur EF) stocke les informations de mappage dans un fichier. edmx au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="2d13d-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="2d13d-108">Au moment de la génération, le Entity Designer utilise les informations d’un fichier. edmx pour créer le fichier. MSL requis par Entity Framework au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d13d-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="2d13d-109">Les noms de tous les types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif.</span><span class="sxs-lookup"><span data-stu-id="2d13d-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="2d13d-110">Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez [spécification CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="2d13d-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="2d13d-111">Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez la [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="2d13d-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="2d13d-112">Les versions de MSL sont différenciées par les espaces de noms XML.</span><span class="sxs-lookup"><span data-stu-id="2d13d-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="2d13d-113">Version MSL</span><span class="sxs-lookup"><span data-stu-id="2d13d-113">MSL Version</span></span> | <span data-ttu-id="2d13d-114">Espace de noms XML</span><span class="sxs-lookup"><span data-stu-id="2d13d-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="2d13d-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="2d13d-115">MSL v1</span></span>      | <span data-ttu-id="2d13d-116">urn : schemas-microsoft-com : Windows : Storage : Mapping : CS</span><span class="sxs-lookup"><span data-stu-id="2d13d-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="2d13d-117">MSL v2</span><span class="sxs-lookup"><span data-stu-id="2d13d-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="2d13d-118">MSL v3</span><span class="sxs-lookup"><span data-stu-id="2d13d-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="2d13d-119">Élément Alias (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-119">Alias Element (MSL)</span></span>

<span data-ttu-id="2d13d-120">L’élément d' **alias** en Mapping Specification Language (MSL) est un enfant de l’élément de mappage utilisé pour définir des alias pour les espaces de noms du modèle conceptuel et du modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="2d13d-121">Les noms de tous les types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif.</span><span class="sxs-lookup"><span data-stu-id="2d13d-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="2d13d-122">Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez Schema, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="2d13d-123">Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez Schema, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="2d13d-124">L’élément **alias** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-125">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-125">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-126">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **alias** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="2d13d-127">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-127">Attribute Name</span></span> | <span data-ttu-id="2d13d-128">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-128">Is Required</span></span> | <span data-ttu-id="2d13d-129">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-130">**Clé**</span><span class="sxs-lookup"><span data-stu-id="2d13d-130">**Key**</span></span>        | <span data-ttu-id="2d13d-131">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-131">Yes</span></span>         | <span data-ttu-id="2d13d-132">Alias de l’espace de noms spécifié par l’attribut **value** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="2d13d-133">**Valeur**</span><span class="sxs-lookup"><span data-stu-id="2d13d-133">**Value**</span></span>      | <span data-ttu-id="2d13d-134">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-134">Yes</span></span>         | <span data-ttu-id="2d13d-135">Espace de noms pour lequel la valeur de l’élément **Key** est un alias.</span><span class="sxs-lookup"><span data-stu-id="2d13d-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="2d13d-136">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-136">Example</span></span>

<span data-ttu-id="2d13d-137">L’exemple suivant montre un élément d' **alias** qui définit un alias, `c` , pour les types définis dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="2d13d-138">AssociationEnd, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="2d13d-139">L’élément **AssociationEnd** en Mapping Specification Language (MSL) est utilisé lorsque les fonctions de modification d’un type d’entité dans le modèle conceptuel sont mappées aux procédures stockées dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="2d13d-140">Si une procédure stockée de modification accepte un paramètre dont la valeur est conservée dans une propriété Association, l’élément **AssociationEnd** mappe la valeur de la propriété au paramètre.</span><span class="sxs-lookup"><span data-stu-id="2d13d-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="2d13d-141">Pour plus d'informations, voir l'exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="2d13d-141">For more information, see the example below.</span></span>

<span data-ttu-id="2d13d-142">Pour plus d’informations sur le mappage des fonctions de modification de types d’entité aux procédures stockées, consultez l’élément ModificationFunctionMapping (MSL) et procédure pas à pas : mappage d’une entité à des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="2d13d-143">L’élément **AssociationEnd** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-145">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-145">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-146">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **AssociationEnd** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="2d13d-147">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-147">Attribute Name</span></span>     | <span data-ttu-id="2d13d-148">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-148">Is Required</span></span> | <span data-ttu-id="2d13d-149">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-150">**AssociationSet ;**</span><span class="sxs-lookup"><span data-stu-id="2d13d-150">**AssociationSet**</span></span> | <span data-ttu-id="2d13d-151">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-151">Yes</span></span>         | <span data-ttu-id="2d13d-152">Nom de l'association mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="2d13d-153">**From**</span><span class="sxs-lookup"><span data-stu-id="2d13d-153">**From**</span></span>           | <span data-ttu-id="2d13d-154">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-154">Yes</span></span>         | <span data-ttu-id="2d13d-155">Valeur de l’attribut **FromRole** de la propriété de navigation qui correspond à l’Association qui est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="2d13d-156">Pour plus d’informations, consultez NavigationProperty, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="2d13d-157">**To**</span><span class="sxs-lookup"><span data-stu-id="2d13d-157">**To**</span></span>             | <span data-ttu-id="2d13d-158">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-158">Yes</span></span>         | <span data-ttu-id="2d13d-159">Valeur de l’attribut **ToRole** de la propriété de navigation qui correspond à l’Association qui est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="2d13d-160">Pour plus d’informations, consultez NavigationProperty, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="2d13d-161">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-161">Example</span></span>

<span data-ttu-id="2d13d-162">Considérons le type d'entité de modèle conceptuel suivant :</span><span class="sxs-lookup"><span data-stu-id="2d13d-162">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="2d13d-163">Considérons également la procédure stockée suivante :</span><span class="sxs-lookup"><span data-stu-id="2d13d-163">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="2d13d-164">Pour mapper la fonction de mise à jour de l' `Course` entité à cette procédure stockée, vous devez fournir une valeur au paramètre **DepartmentID** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="2d13d-165">La valeur pour `DepartmentID` ne correspond pas à une propriété sur le type d'entité ; elle est contenue dans une association indépendante dont le mappage est indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="2d13d-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="2d13d-166">Le code suivant montre l’élément **AssociationEnd** utilisé pour mapper la propriété **DepartmentID** de l’Association de \*\* \_ \_ service de cours FK\*\* à la procédure stockée **UpdateCourse** (à laquelle la fonction de mise à jour du type d’entité **course** est mappée) :</span><span class="sxs-lookup"><span data-stu-id="2d13d-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="2d13d-167">AssociationSetMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-168">L’élément **AssociationSetMapping** en Mapping Specification Language (MSL) définit le mappage entre une association dans le modèle conceptuel et les colonnes de table dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="2d13d-169">Les associations dans le modèle conceptuel sont des types dont les propriétés représentent des colonnes de clé primaire et de clé étrangère dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="2d13d-170">L’élément **AssociationSetMapping** utilise deux éléments EndProperty pour définir les mappages entre les propriétés de type d’association et les colonnes de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="2d13d-171">Vous pouvez placer des conditions sur ces mappages avec l'élément Condition.</span><span class="sxs-lookup"><span data-stu-id="2d13d-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="2d13d-172">Mappez les fonctions d'insertion, de mise à jour et de suppression pour les associer aux procédures stockées dans la base de données avec l'élément ModificationFunctionMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="2d13d-173">Définir des mappages en lecture seule entre des associations et des colonnes de table à l’aide d’une chaîne de Entity SQL dans un élément QueryView.</span><span class="sxs-lookup"><span data-stu-id="2d13d-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-174">Si une contrainte référentielle est définie pour une association dans le modèle conceptuel, l’Association n’a pas besoin d’être mappée avec un élément **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="2d13d-175">Si un élément **AssociationSetMapping** est présent pour une association qui a une contrainte référentielle, les mappages définis dans l’élément **AssociationSetMapping** seront ignorés.</span><span class="sxs-lookup"><span data-stu-id="2d13d-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="2d13d-176">Pour plus d’informations, consultez ReferentialConstraint, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="2d13d-177">L’élément **AssociationSetMapping** peut avoir les éléments enfants suivants</span><span class="sxs-lookup"><span data-stu-id="2d13d-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="2d13d-178">QueryView (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="2d13d-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="2d13d-179">EndProperty (zéro ou deux éléments)</span><span class="sxs-lookup"><span data-stu-id="2d13d-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="2d13d-180">Condition (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="2d13d-181">ModificationFunctionMapping (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="2d13d-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-182">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-182">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-183">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="2d13d-184">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-184">Attribute Name</span></span>     | <span data-ttu-id="2d13d-185">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-185">Is Required</span></span> | <span data-ttu-id="2d13d-186">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-187">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-187">**Name**</span></span>           | <span data-ttu-id="2d13d-188">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-188">Yes</span></span>         | <span data-ttu-id="2d13d-189">Nom de l'ensemble d'associations du modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="2d13d-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-190">**TypeName**</span></span>       | <span data-ttu-id="2d13d-191">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-191">No</span></span>          | <span data-ttu-id="2d13d-192">Nom qualifié par un espace de noms du type d'association du modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="2d13d-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="2d13d-193">**StoreEntitySet**</span></span> | <span data-ttu-id="2d13d-194">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-194">No</span></span>          | <span data-ttu-id="2d13d-195">Nom de la table mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="2d13d-196">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-196">Example</span></span>

<span data-ttu-id="2d13d-197">L’exemple suivant montre un élément **AssociationSetMapping** dans lequel l’Association de \*\* \_ \_ service de cours FK\*\* définie dans le modèle conceptuel est mappée à la table **course** de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="2d13d-198">Les mappages entre les propriétés de type d’association et les colonnes de table sont spécifiés dans les éléments **EndProperty** enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="2d13d-199">ComplexProperty, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="2d13d-200">Un élément **ComplexProperty** en Mapping Specification Language (MSL) définit le mappage entre une propriété de type complexe sur un type d’entité de modèle conceptuel et des colonnes de table dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="2d13d-201">Les mappages de colonnes de propriété sont spécifiés dans des éléments ScalarProperty enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="2d13d-202">L’élément de propriété **complexType** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-203">ScalarProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="2d13d-204">**ComplexProperty** (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="2d13d-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="2d13d-205">ComplextTypeMapping (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="2d13d-206">Condition (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-207">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-207">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-208">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ComplexProperty** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="2d13d-209">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-209">Attribute Name</span></span> | <span data-ttu-id="2d13d-210">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-210">Is Required</span></span> | <span data-ttu-id="2d13d-211">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-212">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-212">**Name**</span></span>       | <span data-ttu-id="2d13d-213">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-213">Yes</span></span>         | <span data-ttu-id="2d13d-214">Nom de la propriété complexe d'un type d'entité dans le modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="2d13d-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-215">**TypeName**</span></span>   | <span data-ttu-id="2d13d-216">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-216">No</span></span>          | <span data-ttu-id="2d13d-217">Nom qualifié par un espace de noms du type de propriété de modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="2d13d-218">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-218">Example</span></span>

<span data-ttu-id="2d13d-219">L'exemple suivant est basé sur le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-219">The following example is based on the School model.</span></span> <span data-ttu-id="2d13d-220">Le type complexe suivant a été ajouté au modèle conceptuel :</span><span class="sxs-lookup"><span data-stu-id="2d13d-220">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="2d13d-221">Les propriétés **LastName** et **FirstName** du type d’entité **Person** ont été remplacées par une propriété complexe, **Name**:</span><span class="sxs-lookup"><span data-stu-id="2d13d-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="2d13d-222">Le MSL suivant montre l’élément **ComplexProperty** utilisé pour mapper la propriété **Name** aux colonnes de la base de données sous-jacente :</span><span class="sxs-lookup"><span data-stu-id="2d13d-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="2d13d-223">ComplexTypeMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-224">L’élément **ComplexTypeMapping,** en Mapping Specification Language (MSL) est un enfant de l’élément ResultMapping et définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée dans la base de données sous-jacente lorsque les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="2d13d-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="2d13d-225">L'importation de fonction retourne un type complexe conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="2d13d-226">Les noms de colonne retournés par la procédure stockée ne correspondent pas exactement aux noms de propriété du type complexe.</span><span class="sxs-lookup"><span data-stu-id="2d13d-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="2d13d-227">Par défaut, le mappage entre les colonnes retournées par une procédure stockée et un type complexe est basé sur les noms de colonne et de propriété.</span><span class="sxs-lookup"><span data-stu-id="2d13d-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="2d13d-228">Si les noms de colonne ne correspondent pas exactement aux noms de propriété, vous devez utiliser l’élément **ComplexTypeMapping,** pour définir le mappage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="2d13d-229">Pour obtenir un exemple du mappage par défaut, consultez élément FunctionImportMapping (MSL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="2d13d-230">L’élément **ComplexTypeMapping,** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-231">ScalarProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-232">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-232">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-233">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ComplexTypeMapping,** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="2d13d-234">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-234">Attribute Name</span></span> | <span data-ttu-id="2d13d-235">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-235">Is Required</span></span> | <span data-ttu-id="2d13d-236">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="2d13d-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-237">**TypeName**</span></span>   | <span data-ttu-id="2d13d-238">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-238">Yes</span></span>         | <span data-ttu-id="2d13d-239">Nom qualifié par un espace de noms du type complexe mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-240">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-240">Example</span></span>

<span data-ttu-id="2d13d-241">Examinez la procédure stockée suivante :</span><span class="sxs-lookup"><span data-stu-id="2d13d-241">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="2d13d-242">De même, considérons le type complexe de modèle conceptuel suivant :</span><span class="sxs-lookup"><span data-stu-id="2d13d-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="2d13d-243">Afin de créer une importation de fonction qui retourne des instances du type complexe précédent, le mappage entre les colonnes retournées par la procédure stockée et le type d’entité doit être défini dans un élément **ComplexTypeMapping,** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="2d13d-244">Condition, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-244">Condition Element (MSL)</span></span>

<span data-ttu-id="2d13d-245">L’élément **condition** en Mapping Specification Language (MSL) place des conditions sur les mappages entre le modèle conceptuel et la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="2d13d-246">Le mappage défini dans un nœud XML est valide si toutes les conditions, comme spécifié dans les éléments de **condition** enfants, sont remplies.</span><span class="sxs-lookup"><span data-stu-id="2d13d-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="2d13d-247">À défaut, le mappage n'est pas valide.</span><span class="sxs-lookup"><span data-stu-id="2d13d-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="2d13d-248">Par exemple, si un élément MappingFragment contient un ou plusieurs éléments enfants **condition** , le mappage défini dans le nœud **MappingFragment** n’est valide que si toutes les conditions des éléments de **condition** enfants sont remplies.</span><span class="sxs-lookup"><span data-stu-id="2d13d-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="2d13d-249">Chaque condition peut s’appliquer à un **nom** (le nom d’une propriété d’entité de modèle conceptuel, spécifié par l’attribut **Name** ) ou à un **ColumnName** (le nom d’une colonne dans la base de données, spécifié par l’attribut **ColumnName** ).</span><span class="sxs-lookup"><span data-stu-id="2d13d-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="2d13d-250">Lorsque l’attribut **Name** est défini, la condition est vérifiée par rapport à une valeur de propriété d’entité.</span><span class="sxs-lookup"><span data-stu-id="2d13d-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="2d13d-251">Lorsque l’attribut **ColumnName** est défini, la condition est vérifiée par rapport à une valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="2d13d-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="2d13d-252">Seul l’un des attributs **Name** ou **ColumnName** peut être spécifié dans un élément **condition** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-253">Lorsque l’élément **condition** est utilisé dans un élément FunctionImportMapping, seul l’attribut **Name** n’est pas applicable.</span><span class="sxs-lookup"><span data-stu-id="2d13d-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="2d13d-254">L’élément **condition** peut être un enfant des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="2d13d-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="2d13d-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-256">ComplexProperty</span></span>
-   <span data-ttu-id="2d13d-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-257">EntitySetMapping</span></span>
-   <span data-ttu-id="2d13d-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="2d13d-258">MappingFragment</span></span>
-   <span data-ttu-id="2d13d-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-259">EntityTypeMapping</span></span>

<span data-ttu-id="2d13d-260">L’élément **condition** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-261">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-261">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-262">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **condition** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="2d13d-263">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-263">Attribute Name</span></span> | <span data-ttu-id="2d13d-264">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-264">Is Required</span></span> | <span data-ttu-id="2d13d-265">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-266">**ColumnName**</span></span> | <span data-ttu-id="2d13d-267">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-267">No</span></span>          | <span data-ttu-id="2d13d-268">Nom de la colonne de table dont la valeur est utilisée pour évaluer la condition.</span><span class="sxs-lookup"><span data-stu-id="2d13d-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="2d13d-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="2d13d-269">**IsNull**</span></span>     | <span data-ttu-id="2d13d-270">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-270">No</span></span>          | <span data-ttu-id="2d13d-271">**True** ou **False**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-271">**True** or **False**.</span></span> <span data-ttu-id="2d13d-272">Si la valeur est **true** et que la valeur de la colonne est **null**, ou si la valeur est **false** et que la valeur de la colonne n’est pas **null**, la condition est true.</span><span class="sxs-lookup"><span data-stu-id="2d13d-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="2d13d-273">Sinon, la condition n'est pas vérifiée (False).</span><span class="sxs-lookup"><span data-stu-id="2d13d-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="2d13d-274">Les attributs **IsNull** et **value** ne peuvent pas être utilisés en même temps.</span><span class="sxs-lookup"><span data-stu-id="2d13d-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="2d13d-275">**Valeur**</span><span class="sxs-lookup"><span data-stu-id="2d13d-275">**Value**</span></span>      | <span data-ttu-id="2d13d-276">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-276">No</span></span>          | <span data-ttu-id="2d13d-277">Valeur à laquelle la valeur de colonne est comparée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-277">The value with which the column value is compared.</span></span> <span data-ttu-id="2d13d-278">Si les valeurs sont identiques, la condition est vérifiée (True).</span><span class="sxs-lookup"><span data-stu-id="2d13d-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="2d13d-279">Sinon, la condition n'est pas vérifiée (False).</span><span class="sxs-lookup"><span data-stu-id="2d13d-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="2d13d-280">Les attributs **IsNull** et **value** ne peuvent pas être utilisés en même temps.</span><span class="sxs-lookup"><span data-stu-id="2d13d-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="2d13d-281">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-281">**Name**</span></span>       | <span data-ttu-id="2d13d-282">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-282">No</span></span>          | <span data-ttu-id="2d13d-283">Nom de la propriété d'entité de modèle conceptuel dont la valeur est utilisée pour évaluer la condition.</span><span class="sxs-lookup"><span data-stu-id="2d13d-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="2d13d-284">Cet attribut n’est pas applicable si l’élément **condition** est utilisé dans un élément FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="2d13d-285">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-285">Example</span></span>

<span data-ttu-id="2d13d-286">L’exemple suivant montre des éléments de **condition** en tant qu’enfants d’éléments **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="2d13d-287">Lorsque **HireDate** n’a pas la valeur null et que **EnrollmentDate** a la valeur null, les données sont mappées entre le type **SchoolModel. Instructor** et les colonnes **PersonID** et **HireDate** de la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="2d13d-288">Lorsque **EnrollmentDate** n’a pas la valeur null et **HireDate** a la valeur null, les données sont mappées entre le type **SchoolModel. Student** et les colonnes **PersonID** et **inscription** de la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="2d13d-289">DeleteFunction, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="2d13d-290">L’élément **DeleteFunction** en Mapping Specification Language (MSL) mappe la fonction Delete d’un type d’entité ou d’une association dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="2d13d-291">Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-292">Pour plus d’informations, consultez Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-293">Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="2d13d-294">Application de DeleteFunction à EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="2d13d-295">Lorsqu’il est appliqué à l’élément EntityTypeMapping, l’élément **DeleteFunction** mappe la fonction Delete d’un type d’entité dans le modèle conceptuel à une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="2d13d-296">L’élément **DeleteFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à un élément **EntityTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="2d13d-297">AssociationEnd (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="2d13d-298">ComplexProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="2d13d-299">ScalarProperty (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-300">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-300">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-301">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **DeleteFunction** lorsqu’il est appliqué à un élément **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="2d13d-302">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-302">Attribute Name</span></span>            | <span data-ttu-id="2d13d-303">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-303">Is Required</span></span> | <span data-ttu-id="2d13d-304">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-305">**FunctionName**</span></span>          | <span data-ttu-id="2d13d-306">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-306">Yes</span></span>         | <span data-ttu-id="2d13d-307">Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de suppression est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="2d13d-308">La procédure stockée doit être déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="2d13d-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="2d13d-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="2d13d-310">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-310">No</span></span>          | <span data-ttu-id="2d13d-311">Nom du paramètre de sortie qui retourne le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="2d13d-312">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-312">Example</span></span>

<span data-ttu-id="2d13d-313">L’exemple suivant est basé sur le modèle School et montre l’élément **DeleteFunction** qui mappe la fonction Delete du type d’entité **Person** à la procédure stockée **DeletePerson** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="2d13d-314">La procédure stockée **DeletePerson** est déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="2d13d-315">Application de DeleteFunction à AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="2d13d-316">Lorsqu’il est appliqué à l’élément AssociationSetMapping, l’élément **DeleteFunction** mappe la fonction Delete d’une association dans le modèle conceptuel à une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="2d13d-317">L’élément **DeleteFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à l’élément **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="2d13d-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-319">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-319">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-320">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **DeleteFunction** lorsqu’il est appliqué à l’élément **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="2d13d-321">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-321">Attribute Name</span></span>            | <span data-ttu-id="2d13d-322">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-322">Is Required</span></span> | <span data-ttu-id="2d13d-323">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-324">**FunctionName**</span></span>          | <span data-ttu-id="2d13d-325">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-325">Yes</span></span>         | <span data-ttu-id="2d13d-326">Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de suppression est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="2d13d-327">La procédure stockée doit être déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="2d13d-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="2d13d-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="2d13d-329">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-329">No</span></span>          | <span data-ttu-id="2d13d-330">Nom du paramètre de sortie qui retourne le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="2d13d-331">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-331">Example</span></span>

<span data-ttu-id="2d13d-332">L’exemple suivant est basé sur le modèle School et montre l’élément **DeleteFunction** utilisé pour mapper la fonction Delete de l’Association **CourseInstructor** à la procédure stockée **DeleteCourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="2d13d-333">La procédure stockée **DeleteCourseInstructor** est déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="2d13d-334">EndProperty, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="2d13d-335">L’élément **EndProperty** en Mapping Specification Language (MSL) définit le mappage entre une terminaison ou une fonction de modification d’une association de modèle conceptuel et la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="2d13d-336">Le mappage de colonne de propriété est spécifié dans un élément ScalarProperty enfant.</span><span class="sxs-lookup"><span data-stu-id="2d13d-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="2d13d-337">Lorsqu’un élément **EndProperty** est utilisé pour définir le mappage de la fin d’une association de modèle conceptuel, il est un enfant d’un élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="2d13d-338">Lorsque l’élément **EndProperty** est utilisé pour définir le mappage d’une fonction de modification d’une association de modèle conceptuel, il est un enfant d’un élément InsertFunction ou d’un élément DeleteFunction.</span><span class="sxs-lookup"><span data-stu-id="2d13d-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="2d13d-339">L’élément **EndProperty** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-340">ScalarProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-341">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-341">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-342">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **EndProperty** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="2d13d-343">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-343">Attribute Name</span></span> | <span data-ttu-id="2d13d-344">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-344">Is Required</span></span> | <span data-ttu-id="2d13d-345">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="2d13d-346">Nom</span><span class="sxs-lookup"><span data-stu-id="2d13d-346">Name</span></span>           | <span data-ttu-id="2d13d-347">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-347">Yes</span></span>         | <span data-ttu-id="2d13d-348">Nom de la terminaison d'association mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-349">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-349">Example</span></span>

<span data-ttu-id="2d13d-350">L’exemple suivant montre un élément **AssociationSetMapping** dans lequel l’Association de \*\* \_ \_ service de cours FK\*\* dans le modèle conceptuel est mappée à la table **course** de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="2d13d-351">Les mappages entre les propriétés de type d’association et les colonnes de table sont spécifiés dans les éléments **EndProperty** enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-352">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-352">Example</span></span>

<span data-ttu-id="2d13d-353">L’exemple suivant montre l’élément **EndProperty** qui mappe les fonctions d’insertion et de suppression d’une association (**CourseInstructor**) à des procédures stockées dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="2d13d-354">Les fonctions mappées sont déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-354">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="2d13d-355">EntityContainerMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-356">L’élément **EntityContainerMapping** en Mapping Specification Language (MSL) mappe le conteneur d’entités du modèle conceptuel au conteneur d’entités dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="2d13d-357">L’élément **EntityContainerMapping** est un enfant de l’élément Mapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="2d13d-358">L’élément **EntityContainerMapping** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="2d13d-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2d13d-359">EntitySetMapping (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="2d13d-360">AssociationSetMapping (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="2d13d-361">FunctionImportMapping (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-362">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-362">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-363">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntityContainerMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="2d13d-364">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-364">Attribute Name</span></span>            | <span data-ttu-id="2d13d-365">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-365">Is Required</span></span> | <span data-ttu-id="2d13d-366">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="2d13d-367">**StorageModelContainer**</span></span> | <span data-ttu-id="2d13d-368">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-368">Yes</span></span>         | <span data-ttu-id="2d13d-369">Nom du conteneur d'entités de modèle de stockage mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="2d13d-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="2d13d-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="2d13d-371">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-371">Yes</span></span>         | <span data-ttu-id="2d13d-372">Nom du conteneur d'entités de modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="2d13d-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="2d13d-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="2d13d-374">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-374">No</span></span>          | <span data-ttu-id="2d13d-375">**True** ou **False**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-375">**True** or **False**.</span></span> <span data-ttu-id="2d13d-376">Si la **valeur est false**, aucune vue de mise à jour n’est générée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="2d13d-377">Cet attribut doit avoir la valeur **false** lorsque vous avez un mappage en lecture seule qui serait non valide, car les données ne peuvent pas aller-retour.</span><span class="sxs-lookup"><span data-stu-id="2d13d-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="2d13d-378">La valeur par défaut est **True**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-379">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-379">Example</span></span>

<span data-ttu-id="2d13d-380">L’exemple suivant montre un élément **EntityContainerMapping** qui mappe le conteneur **SchoolModelEntities** (le conteneur d’entités de modèle conceptuel) au conteneur **SchoolModelStoreContainer** (conteneur d’entités de modèle de stockage) :</span><span class="sxs-lookup"><span data-stu-id="2d13d-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="2d13d-381">EntitySetMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-382">L’élément **EntitySetMapping** en Mapping Specification Language (MSL) mappe tous les types dans un jeu d’entités de modèle conceptuel aux jeux d’entités dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="2d13d-383">Un jeu d'entités dans le modèle conceptuel est un conteneur logique pour instances d'entités de même type (et des types dérivés).</span><span class="sxs-lookup"><span data-stu-id="2d13d-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="2d13d-384">Un jeu d'entités dans le modèle de stockage représente une table ou une vue de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="2d13d-385">Le jeu d’entités du modèle conceptuel est spécifié par la valeur de l’attribut **Name** de l’élément **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="2d13d-386">La table ou la vue mappée à est spécifiée par l’attribut **StoreEntitySet** dans chaque élément MappingFragment enfant ou dans l’élément **EntitySetMapping** lui-même.</span><span class="sxs-lookup"><span data-stu-id="2d13d-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="2d13d-387">L’élément **EntitySetMapping** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-388">EntityTypeMapping (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="2d13d-389">QueryView (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="2d13d-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="2d13d-390">MappingFragment (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-391">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-391">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-392">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="2d13d-393">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-393">Attribute Name</span></span>           | <span data-ttu-id="2d13d-394">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-394">Is Required</span></span> | <span data-ttu-id="2d13d-395">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-396">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-396">**Name**</span></span>                 | <span data-ttu-id="2d13d-397">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-397">Yes</span></span>         | <span data-ttu-id="2d13d-398">Nom du jeu d'entités de modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="2d13d-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="2d13d-399">**TypeName** **1**</span></span>       | <span data-ttu-id="2d13d-400">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-400">No</span></span>          | <span data-ttu-id="2d13d-401">Nom du type d'entité de modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="2d13d-402">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="2d13d-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="2d13d-403">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-403">No</span></span>          | <span data-ttu-id="2d13d-404">Nom du jeu d'entités de modèle de stockage de destination du mappage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="2d13d-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="2d13d-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="2d13d-406">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-406">No</span></span>          | <span data-ttu-id="2d13d-407">**True** ou **false** selon que seules des lignes distinctes sont retournées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="2d13d-408">Si cet attribut est défini sur **true**, l’attribut **GenerateUpdateViews** de l’élément EntityContainerMapping doit avoir la valeur **false**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="2d13d-409">**1** les attributs **TypeName** et **StoreEntitySet** peuvent être utilisés à la place des éléments enfants EntityTypeMapping et MappingFragment pour mapper un type d’entité unique à une table unique.</span><span class="sxs-lookup"><span data-stu-id="2d13d-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="2d13d-410">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-410">Example</span></span>

<span data-ttu-id="2d13d-411">L’exemple suivant montre un élément **EntitySetMapping** qui mappe trois types (un type de base et deux types dérivés) dans le jeu d’entités **courses** du modèle conceptuel à trois tables différentes dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="2d13d-412">Les tables sont spécifiées par l’attribut **StoreEntitySet** dans chaque élément **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="2d13d-413">EntityTypeMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-414">L’élément **EntityTypeMapping** en Mapping Specification Language (MSL) définit le mappage entre un type d’entité dans le modèle conceptuel et les tables ou vues dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="2d13d-415">Pour plus d'informations sur les types d'entité de modèle conceptuel et les tables ou les vues de base de données sous-jacente, consultez Élément EntityType (CSDL) et Élément EntitySet (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="2d13d-416">Le type d’entité de modèle conceptuel qui est mappé est spécifié par l’attribut **TypeName** de l’élément **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="2d13d-417">La table ou la vue mappée est spécifiée par l’attribut **StoreEntitySet** de l’élément MappingFragment enfant.</span><span class="sxs-lookup"><span data-stu-id="2d13d-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="2d13d-418">L'élément enfant ModificationFunctionMapping peut être utilisé pour mapper les fonctions d'insertion, de mise à jour ou de suppression de types d'entités aux procédures stockées de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="2d13d-419">L’élément **EntityTypeMapping** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-420">MappingFragment (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="2d13d-421">ModificationFunctionMapping (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="2d13d-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="2d13d-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-422">ScalarProperty</span></span>
-   <span data-ttu-id="2d13d-423">Condition</span><span class="sxs-lookup"><span data-stu-id="2d13d-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-424">Les éléments **MappingFragment** et **ModificationFunctionMapping** ne peuvent pas être des éléments enfants de l’élément **EntityTypeMapping** en même temps.</span><span class="sxs-lookup"><span data-stu-id="2d13d-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="2d13d-425">Les éléments **ScalarProperty** et **condition** ne peuvent être que des éléments enfants de l’élément **EntityTypeMapping** lorsqu’il est utilisé dans un élément FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-426">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-426">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-427">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="2d13d-428">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-428">Attribute Name</span></span> | <span data-ttu-id="2d13d-429">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-429">Is Required</span></span> | <span data-ttu-id="2d13d-430">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-431">**TypeName**</span></span>   | <span data-ttu-id="2d13d-432">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-432">Yes</span></span>         | <span data-ttu-id="2d13d-433">Nom qualifié par un espace de noms du type d'entité de modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="2d13d-434">Si le type correspond à un type abstrait ou dérivé, la valeur doit être `IsOfType(Namespace-qualified_type_name)`.</span><span class="sxs-lookup"><span data-stu-id="2d13d-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-435">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-435">Example</span></span>

<span data-ttu-id="2d13d-436">L’exemple suivant montre un élément EntitySetMapping avec deux éléments **EntityTypeMapping** enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="2d13d-437">Dans le premier élément **EntityTypeMapping** , le type d’entité **SchoolModel. Person** est mappé à la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="2d13d-438">Dans le deuxième élément **EntityTypeMapping** , la fonctionnalité de mise à jour du type **SchoolModel. Person** est mappée à une procédure stockée, **UpdatePerson**, dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-439">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-439">Example</span></span>

<span data-ttu-id="2d13d-440">L'exemple suivant illustre le mappage d'une hiérarchie de types dont le type racine est abstrait.</span><span class="sxs-lookup"><span data-stu-id="2d13d-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="2d13d-441">Notez l’utilisation de la `IsOfType` syntaxe pour les attributs **TypeName** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="2d13d-442">FunctionImportMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-443">L’élément **FunctionImportMapping** en Mapping Specification Language (MSL) définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée ou une fonction dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="2d13d-444">Les importations de fonction doivent être déclarées dans le modèle conceptuel et les procédures stockées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-445">Pour plus d’informations, consultez FunctionImport, élément (CSDL) et Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-446">Par défaut, si une importation de fonction retourne un type d'entité ou un type complexe de modèle conceptuel, les noms des colonnes retournés par la procédure stockée sous-jacente doivent correspondre exactement aux noms des propriétés sur le type de modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="2d13d-447">Si les noms de colonne ne correspondent pas exactement aux noms de propriété, le mappage doit être défini dans un élément ResultMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="2d13d-448">L’élément **FunctionImportMapping** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-449">ResultMapping (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="2d13d-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-450">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-450">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-451">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **FunctionImportMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="2d13d-452">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-452">Attribute Name</span></span>         | <span data-ttu-id="2d13d-453">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-453">Is Required</span></span> | <span data-ttu-id="2d13d-454">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-455">**FunctionImportName**</span></span> | <span data-ttu-id="2d13d-456">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-456">Yes</span></span>         | <span data-ttu-id="2d13d-457">Nom de l'importation de fonction dans le modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="2d13d-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-458">**FunctionName**</span></span>       | <span data-ttu-id="2d13d-459">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-459">Yes</span></span>         | <span data-ttu-id="2d13d-460">Nom qualifié par un espace de noms de la fonction dans le modèle de stockage mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-461">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-461">Example</span></span>

<span data-ttu-id="2d13d-462">L'exemple suivant est basé sur le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-462">The following example is based on the School model.</span></span> <span data-ttu-id="2d13d-463">Considérez la fonction suivante dans le modèle de stockage :</span><span class="sxs-lookup"><span data-stu-id="2d13d-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="2d13d-464">Considérez également cette importation de fonction dans le modèle conceptuel :</span><span class="sxs-lookup"><span data-stu-id="2d13d-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="2d13d-465">L’exemple suivant montre un élément **FunctionImportMapping** utilisé pour mapper la fonction et l’importation de fonction ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="2d13d-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="2d13d-466">InsertFunction, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="2d13d-467">L’élément **InsertFunction** en Mapping Specification Language (MSL) mappe la fonction d’insertion d’un type d’entité ou d’une association dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="2d13d-468">Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-469">Pour plus d’informations, consultez Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-470">Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="2d13d-471">L’élément **InsertFunction** peut être un enfant de l’élément ModificationFunctionMapping et être appliqué à l’élément EntityTypeMapping ou à l’élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="2d13d-472">Application d'InsertFunction à EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="2d13d-473">Lorsqu’il est appliqué à l’élément EntityTypeMapping, l’élément **InsertFunction** mappe la fonction d’insertion d’un type d’entité dans le modèle conceptuel à une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="2d13d-474">L’élément **InsertFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à un élément **EntityTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="2d13d-475">AssociationEnd (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="2d13d-476">ComplexProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="2d13d-477">ResultBinding (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="2d13d-478">ScalarProperty (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-479">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-479">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-480">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **InsertFunction** lorsqu’ils sont appliqués à un élément **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="2d13d-481">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-481">Attribute Name</span></span>            | <span data-ttu-id="2d13d-482">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-482">Is Required</span></span> | <span data-ttu-id="2d13d-483">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-484">**FunctionName**</span></span>          | <span data-ttu-id="2d13d-485">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-485">Yes</span></span>         | <span data-ttu-id="2d13d-486">Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction d'insertion est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="2d13d-487">La procédure stockée doit être déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="2d13d-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="2d13d-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="2d13d-489">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-489">No</span></span>          | <span data-ttu-id="2d13d-490">Nom du paramètre de sortie qui retourne le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="2d13d-491">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-491">Example</span></span>

<span data-ttu-id="2d13d-492">L’exemple suivant est basé sur le modèle School et montre l’élément **InsertFunction** utilisé pour mapper la fonction d’insertion du type d’entité Person à la procédure stockée **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="2d13d-493">La procédure stockée **InsertPerson** est déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="2d13d-494">Application d'InsertFunction à AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="2d13d-495">Lorsqu’il est appliqué à l’élément AssociationSetMapping, l’élément **InsertFunction** mappe la fonction d’insertion d’une association dans le modèle conceptuel à une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="2d13d-496">L’élément **InsertFunction** peut avoir les éléments enfants suivants lorsqu’il est appliqué à l’élément **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="2d13d-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-498">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-498">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-499">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **InsertFunction** lorsqu’il est appliqué à l’élément **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="2d13d-500">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-500">Attribute Name</span></span>            | <span data-ttu-id="2d13d-501">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-501">Is Required</span></span> | <span data-ttu-id="2d13d-502">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-503">**FunctionName**</span></span>          | <span data-ttu-id="2d13d-504">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-504">Yes</span></span>         | <span data-ttu-id="2d13d-505">Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction d'insertion est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="2d13d-506">La procédure stockée doit être déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="2d13d-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="2d13d-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="2d13d-508">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-508">No</span></span>          | <span data-ttu-id="2d13d-509">Nom du paramètre de sortie qui retourne le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="2d13d-510">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-510">Example</span></span>

<span data-ttu-id="2d13d-511">L’exemple suivant est basé sur le modèle School et montre l’élément **InsertFunction** utilisé pour mapper la fonction d’insertion de l’Association **CourseInstructor** à la procédure stockée **InsertCourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="2d13d-512">La procédure stockée **InsertCourseInstructor** est déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="2d13d-513">Élément de mappage (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-514">L’élément **Mapping** en Mapping Specification Language (MSL) contient des informations pour le mappage d’objets définis dans un modèle conceptuel à une base de données (comme décrit dans un modèle de stockage).</span><span class="sxs-lookup"><span data-stu-id="2d13d-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="2d13d-515">Pour plus d’informations, consultez Spécification CSDL et spécification SSDL.</span><span class="sxs-lookup"><span data-stu-id="2d13d-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="2d13d-516">L’élément **Mapping** est l’élément racine d’une spécification de mappage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="2d13d-517">L’espace de noms XML pour les spécifications de mappage est https://schemas.microsoft.com/ado/2009/11/mapping/cs .</span><span class="sxs-lookup"><span data-stu-id="2d13d-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="2d13d-518">L'élément de mappage peut avoir les éléments enfants suivants (dans l'ordre répertorié) :</span><span class="sxs-lookup"><span data-stu-id="2d13d-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2d13d-519">Alias (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="2d13d-520">EntityContainerMapping (exactement un élément).</span><span class="sxs-lookup"><span data-stu-id="2d13d-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="2d13d-521">Les noms de types de modèle conceptuel et de stockage référencés en MSL doivent être qualifiés par le nom de leur espace de noms respectif.</span><span class="sxs-lookup"><span data-stu-id="2d13d-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="2d13d-522">Pour plus d’informations sur le nom de l’espace de noms du modèle conceptuel, consultez Schema, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="2d13d-523">Pour plus d’informations sur le nom de l’espace de noms du modèle de stockage, consultez Schema, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="2d13d-524">Les alias d'espace de noms utilisés en MSL peuvent être définis avec l'élément Alias.</span><span class="sxs-lookup"><span data-stu-id="2d13d-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-525">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-525">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-526">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Mapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="2d13d-527">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-527">Attribute Name</span></span> | <span data-ttu-id="2d13d-528">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-528">Is Required</span></span> | <span data-ttu-id="2d13d-529">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="2d13d-530">**Espace**</span><span class="sxs-lookup"><span data-stu-id="2d13d-530">**Space**</span></span>      | <span data-ttu-id="2d13d-531">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-531">Yes</span></span>         | <span data-ttu-id="2d13d-532">**C-S**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-532">**C-S**.</span></span> <span data-ttu-id="2d13d-533">Il s'agit d'une valeur fixe qui ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-534">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-534">Example</span></span>

<span data-ttu-id="2d13d-535">L’exemple suivant illustre un élément de **mappage** basé sur une partie du modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="2d13d-536">Pour plus d’informations sur le modèle School, consultez démarrage rapide (Entity Framework) :</span><span class="sxs-lookup"><span data-stu-id="2d13d-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="2d13d-537">MappingFragment, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="2d13d-538">L’élément **MappingFragment** en Mapping Specification Language (MSL) définit le mappage entre les propriétés d’un type d’entité de modèle conceptuel et une table ou une vue dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="2d13d-539">Pour plus d'informations sur les types d'entité de modèle conceptuel et les tables ou les vues de base de données sous-jacente, consultez Élément EntityType (CSDL) et Élément EntitySet (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="2d13d-540">L’élément **MappingFragment** peut être un élément enfant de l’élément EntityTypeMapping ou de l’élément EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="2d13d-541">L’élément **MappingFragment** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-542">ComplexType (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="2d13d-543">ScalarProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="2d13d-544">Condition (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-545">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-545">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-546">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="2d13d-547">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-547">Attribute Name</span></span>          | <span data-ttu-id="2d13d-548">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-548">Is Required</span></span> | <span data-ttu-id="2d13d-549">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="2d13d-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="2d13d-551">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-551">Yes</span></span>         | <span data-ttu-id="2d13d-552">Nom de la table ou de la vue mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="2d13d-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="2d13d-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="2d13d-554">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-554">No</span></span>          | <span data-ttu-id="2d13d-555">**True** ou **false** selon que seules des lignes distinctes sont retournées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="2d13d-556">Si cet attribut est défini sur **true**, l’attribut **GenerateUpdateViews** de l’élément EntityContainerMapping doit avoir la valeur **false**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-557">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-557">Example</span></span>

<span data-ttu-id="2d13d-558">L’exemple suivant montre un élément **MappingFragment** en tant qu’enfant d’un élément **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="2d13d-559">Dans cet exemple, les propriétés du type de **cours** dans le modèle conceptuel sont mappées aux colonnes de la table **course** dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-560">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-560">Example</span></span>

<span data-ttu-id="2d13d-561">L’exemple suivant montre un élément **MappingFragment** en tant qu’enfant d’un élément **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="2d13d-562">Comme dans l’exemple ci-dessus, les propriétés du type de **cours** dans le modèle conceptuel sont mappées aux colonnes de la table **course** dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="2d13d-563">ModificationFunctionMapping, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-564">L’élément **ModificationFunctionMapping** en Mapping Specification Language (MSL) mappe les fonctions d’insertion, de mise à jour et de suppression d’un type d’entité de modèle conceptuel aux procédures stockées dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="2d13d-565">L’élément **ModificationFunctionMapping** peut également mapper les fonctions d’insertion et de suppression pour les associations plusieurs-à-plusieurs dans le modèle conceptuel aux procédures stockées dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="2d13d-566">Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-567">Pour plus d’informations, consultez Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-568">Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="2d13d-569">Si les fonctions de modification pour une entité dans une hiérarchie d'héritage sont mappées aux procédures stockées, les fonctions de modification de tous les types dans la hiérarchie doivent être mappées aux procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="2d13d-570">L’élément **ModificationFunctionMapping** peut être un enfant de l’élément EntityTypeMapping ou de l’élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="2d13d-571">L’élément **ModificationFunctionMapping** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-572">DeleteFunction (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="2d13d-573">InsertFunction (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="2d13d-574">UpdateFunction (zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="2d13d-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="2d13d-575">Aucun attribut n’est applicable à l’élément **ModificationFunctionMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="2d13d-576">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-576">Example</span></span>

<span data-ttu-id="2d13d-577">L’exemple suivant montre le mappage de jeu d’entités pour le jeu d’entités **People** dans le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="2d13d-578">En plus du mappage de colonnes pour le type d’entité **Person** , le mappage des fonctions d’insertion, de mise à jour et de suppression du type **Person** est affiché.</span><span class="sxs-lookup"><span data-stu-id="2d13d-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="2d13d-579">Les fonctions mappées sont déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-579">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-580">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-580">Example</span></span>

<span data-ttu-id="2d13d-581">L’exemple suivant montre le mappage de l’ensemble d’associations pour l’ensemble d’associations **CourseInstructor** dans le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="2d13d-582">En plus du mappage de colonnes pour l’Association **CourseInstructor** , le mappage des fonctions d’insertion et de suppression de l’Association **CourseInstructor** est affiché.</span><span class="sxs-lookup"><span data-stu-id="2d13d-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="2d13d-583">Les fonctions mappées sont déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-583">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="2d13d-584">QueryView, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="2d13d-585">L’élément **QueryView** en Mapping Specification Language (MSL) définit un mappage en lecture seule entre un type d’entité ou une association dans le modèle conceptuel et une table dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="2d13d-586">Le mappage est défini avec une requête Entity SQL qui est évaluée par rapport au modèle de stockage, et vous exprimez le jeu de résultats en termes d’entité ou d’association dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="2d13d-587">Les affichages des requêtes étant en lecture seule, les types qu'ils définissent ne peuvent pas être mis à jour au moyen des commandes de mise à jour standard.</span><span class="sxs-lookup"><span data-stu-id="2d13d-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="2d13d-588">Les mises à jour de ces types peuvent être effectuées au moyen de fonctions de modification.</span><span class="sxs-lookup"><span data-stu-id="2d13d-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="2d13d-589">Pour plus d’informations, consultez Comment : mapper des fonctions de modification à des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-590">Dans l’élément **QueryView** , Entity SQL expressions qui contiennent des propriétés **GroupBy**, Group ou de navigation ne sont pas prises en charge.</span><span class="sxs-lookup"><span data-stu-id="2d13d-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="2d13d-591">L’élément **QueryView** peut être un enfant de l’élément EntitySetMapping ou de l’élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="2d13d-592">Dans le cas précédent, l'affichage des requêtes définit un mappage en lecture seule pour une entité dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="2d13d-593">Dans le cas précédent, l'affichage des requêtes définit un mappage en lecture seule pour une association dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-594">Si l’élément **AssociationSetMapping** est destiné à une association avec une contrainte référentielle, l’élément **AssociationSetMapping** est ignoré.</span><span class="sxs-lookup"><span data-stu-id="2d13d-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="2d13d-595">Pour plus d’informations, consultez ReferentialConstraint, élément (CSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="2d13d-596">L’élément **QueryView** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-597">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-597">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-598">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **QueryView** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="2d13d-599">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-599">Attribute Name</span></span> | <span data-ttu-id="2d13d-600">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-600">Is Required</span></span> | <span data-ttu-id="2d13d-601">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-602">**TypeName**</span></span>   | <span data-ttu-id="2d13d-603">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-603">No</span></span>          | <span data-ttu-id="2d13d-604">Nom du type de modèle conceptuel mappé par l'affichage des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2d13d-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-605">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-605">Example</span></span>

<span data-ttu-id="2d13d-606">L’exemple suivant montre l’élément **QueryView** en tant qu’enfant de l’élément **EntitySetMapping** et définit un mappage d’affichage des requêtes pour le type d’entité **Department** dans le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="2d13d-607">Étant donné que la requête retourne uniquement un sous-ensemble des membres du type de **service** dans le modèle de stockage, le type de **service** du modèle School a été modifié en fonction de ce mappage comme suit :</span><span class="sxs-lookup"><span data-stu-id="2d13d-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-608">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-608">Example</span></span>

<span data-ttu-id="2d13d-609">L’exemple suivant montre l’élément **QueryView** en tant qu’enfant d’un élément **AssociationSetMapping** et définit un mappage en lecture seule pour l' `FK_Course_Department` Association dans le modèle School.</span><span class="sxs-lookup"><span data-stu-id="2d13d-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="2d13d-610">Commentaires</span><span class="sxs-lookup"><span data-stu-id="2d13d-610">Comments</span></span>

<span data-ttu-id="2d13d-611">Vous pouvez définir des affichages des requêtes pour activer les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="2d13d-612">Définir une entité du modèle conceptuel qui n'inclut pas toutes les propriétés de l'entité dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="2d13d-613">Cela comprend les propriétés qui n’ont pas de valeurs par défaut et qui ne prennent pas en charge les valeurs **null** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="2d13d-614">Mapper des colonnes calculées du modèle de stockage aux propriétés de types d'entités du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="2d13d-615">Définir un mappage dans lequel les conditions utilisées pour partitionner des entités du modèle conceptuel ne sont pas basées sur l'égalité.</span><span class="sxs-lookup"><span data-stu-id="2d13d-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="2d13d-616">Lorsque vous spécifiez un mappage conditionnel à l’aide de l’élément **condition** , la condition fournie doit être égale à la valeur spécifiée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="2d13d-617">Pour plus d’informations, consultez élément condition (MSL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="2d13d-618">Mapper la même colonne du modèle de stockage à plusieurs types du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="2d13d-619">Mapper plusieurs types à la même table.</span><span class="sxs-lookup"><span data-stu-id="2d13d-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="2d13d-620">Définir des associations dans le modèle conceptuel qui ne sont pas basées sur des clés étrangères du schéma relationnel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="2d13d-621">Utiliser une logique métier personnalisée pour définir la valeur de propriétés du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="2d13d-622">Par exemple, vous pouvez mapper la valeur de chaîne « T » dans la source de données à la valeur **true**, une valeur booléenne, dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="2d13d-623">Définir des filtres conditionnels pour les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="2d13d-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="2d13d-624">Appliquer moins de restrictions sur les données dans le modèle conceptuel que dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="2d13d-625">Par exemple, vous pouvez rendre une propriété dans le modèle conceptuel Nullable même si la colonne à laquelle elle est mappée ne prend pas en charge les valeurs **null**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="2d13d-626">Vous devez tenir compte des points suivants lorsque vous définissez des affichages des requêtes pour les entités :</span><span class="sxs-lookup"><span data-stu-id="2d13d-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="2d13d-627">Les affichages des requêtes sont en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="2d13d-627">Query views are read-only.</span></span> <span data-ttu-id="2d13d-628">Les mises à jour des entités ne peuvent être effectuées qu'au moyen de fonctions de modification.</span><span class="sxs-lookup"><span data-stu-id="2d13d-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="2d13d-629">Lorsque vous définissez un type d'entité par un affichage des requêtes, vous devez également définir toutes les entités associées par les affichages des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2d13d-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="2d13d-630">Lorsque vous mappez une association plusieurs-à-plusieurs à une entité dans le modèle de stockage qui représente une table de liens dans le schéma relationnel, vous devez définir un élément **QueryView** dans l’élément **AssociationSetMapping** pour cette table de liens.</span><span class="sxs-lookup"><span data-stu-id="2d13d-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="2d13d-631">Les affichages des requêtes doivent être définis pour tous les types d'une hiérarchie des types.</span><span class="sxs-lookup"><span data-stu-id="2d13d-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="2d13d-632">Pour ce faire, vous pouvez procéder de différentes façons :</span><span class="sxs-lookup"><span data-stu-id="2d13d-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="2d13d-633">Avec un seul élément **QueryView** qui spécifie une seule requête Entity SQL qui retourne une Union de tous les types d’entité dans la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="2d13d-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="2d13d-634">Avec un seul élément **QueryView** qui spécifie une requête Entity SQL unique qui utilise l’opérateur case pour retourner un type d’entité spécifique dans la hiérarchie en fonction d’une condition spécifique.</span><span class="sxs-lookup"><span data-stu-id="2d13d-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="2d13d-635">Avec un élément **QueryView** supplémentaire pour un type spécifique dans la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="2d13d-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="2d13d-636">Dans ce cas, utilisez l’attribut **TypeName** de l’élément **QueryView** pour spécifier le type d’entité pour chaque vue.</span><span class="sxs-lookup"><span data-stu-id="2d13d-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="2d13d-637">Quand une vue de requête est définie, vous ne pouvez pas spécifier l’attribut **StorageSetName** sur l’élément **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="2d13d-638">Quand une vue de requête est définie, l’élément **EntitySetMapping**ne peut pas contenir également des mappages de **Propriétés** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="2d13d-639">Élément ResultBinding (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="2d13d-640">L’élément **ResultBinding** en Mapping Specification Language (MSL) mappe les valeurs de colonne retournées par les procédures stockées aux propriétés d’entité dans le modèle conceptuel lorsque les fonctions de modification de type d’entité sont mappées aux procédures stockées dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="2d13d-641">Par exemple, lorsque la valeur d’une colonne d’identité est retournée par une procédure stockée Insert, l’élément **ResultBinding** mappe la valeur retournée à une propriété de type d’entité dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="2d13d-642">L’élément **ResultBinding** peut être un enfant de l’élément InsertFunction ou de l’élément UpdateFunction.</span><span class="sxs-lookup"><span data-stu-id="2d13d-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="2d13d-643">L’élément **ResultBinding** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-644">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-644">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-645">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ResultBinding** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="2d13d-646">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-646">Attribute Name</span></span> | <span data-ttu-id="2d13d-647">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-647">Is Required</span></span> | <span data-ttu-id="2d13d-648">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-649">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-649">**Name**</span></span>       | <span data-ttu-id="2d13d-650">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-650">Yes</span></span>         | <span data-ttu-id="2d13d-651">Nom de la propriété d'entité dans le modèle conceptuel mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="2d13d-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-652">**ColumnName**</span></span> | <span data-ttu-id="2d13d-653">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-653">Yes</span></span>         | <span data-ttu-id="2d13d-654">Nom de la colonne mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="2d13d-655">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-655">Example</span></span>

<span data-ttu-id="2d13d-656">L’exemple suivant est basé sur le modèle School et montre un élément **InsertFunction** utilisé pour mapper la fonction insert du type d’entité **Person** à la procédure stockée **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="2d13d-657">(La procédure stockée **InsertPerson** est indiquée ci-dessous et est déclarée dans le modèle de stockage.) Un élément **ResultBinding** est utilisé pour mapper une valeur de colonne retournée par la procédure stockée (**NewPersonID**) à une propriété de type d’entité (**PersonID**).</span><span class="sxs-lookup"><span data-stu-id="2d13d-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="2d13d-658">Le code Transact-SQL suivant décrit la procédure stockée **InsertPerson** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="2d13d-659">Élément ResultMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="2d13d-660">L’élément **ResultMapping** en Mapping Specification Language (MSL) définit le mappage entre une importation de fonction dans le modèle conceptuel et une procédure stockée dans la base de données sous-jacente lorsque les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="2d13d-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="2d13d-661">L'importation de fonction retourne un type d'entité de modèle conceptuel ou le type complexe.</span><span class="sxs-lookup"><span data-stu-id="2d13d-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="2d13d-662">Les noms des colonnes retournés par la procédure stockée ne correspondent pas exactement aux noms des propriétés sur le type d'entité ou le type complexe.</span><span class="sxs-lookup"><span data-stu-id="2d13d-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="2d13d-663">Par défaut, le mappage entre les colonnes retournées par une procédure stockée et un type d'entité ou un type complexe est basé sur les noms de colonne et de propriété.</span><span class="sxs-lookup"><span data-stu-id="2d13d-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="2d13d-664">Si les noms de colonne ne correspondent pas exactement aux noms de propriété, vous devez utiliser l’élément **ResultMapping** pour définir le mappage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="2d13d-665">Pour obtenir un exemple du mappage par défaut, consultez élément FunctionImportMapping (MSL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="2d13d-666">L’élément **ResultMapping** est un élément enfant de l’élément FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="2d13d-667">L’élément **ResultMapping** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-668">EntityTypeMapping (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="2d13d-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-669">ComplexTypeMapping</span></span>

<span data-ttu-id="2d13d-670">Aucun attribut n’est applicable à l’élément **ResultMapping** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="2d13d-671">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-671">Example</span></span>

<span data-ttu-id="2d13d-672">Examinez la procédure stockée suivante :</span><span class="sxs-lookup"><span data-stu-id="2d13d-672">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="2d13d-673">De même, considérons le type d'entité de modèle conceptuel suivant :</span><span class="sxs-lookup"><span data-stu-id="2d13d-673">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="2d13d-674">Afin de créer une importation de fonction qui retourne des instances du type d’entité précédent, le mappage entre les colonnes retournées par la procédure stockée et le type d’entité doit être défini dans un élément **ResultMapping** :</span><span class="sxs-lookup"><span data-stu-id="2d13d-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="2d13d-675">ScalarProperty, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="2d13d-676">L’élément **ScalarProperty** en Mapping Specification Language (MSL) mappe une propriété sur un type d’entité de modèle conceptuel, un type complexe ou une association à une colonne de table ou un paramètre de procédure stockée dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="2d13d-677">Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-678">Pour plus d’informations, consultez Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="2d13d-679">L’élément **ScalarProperty** peut être un enfant des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="2d13d-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="2d13d-680">MappingFragment</span></span>
-   <span data-ttu-id="2d13d-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="2d13d-681">InsertFunction</span></span>
-   <span data-ttu-id="2d13d-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="2d13d-682">UpdateFunction</span></span>
-   <span data-ttu-id="2d13d-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="2d13d-683">DeleteFunction</span></span>
-   <span data-ttu-id="2d13d-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-684">EndProperty</span></span>
-   <span data-ttu-id="2d13d-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="2d13d-685">ComplexProperty</span></span>
-   <span data-ttu-id="2d13d-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="2d13d-686">ResultMapping</span></span>

<span data-ttu-id="2d13d-687">En tant qu’enfant de l’élément **MappingFragment**, **ComplexProperty**ou **EndProperty** , l’élément **ScalarProperty** mappe une propriété du modèle conceptuel à une colonne de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="2d13d-688">En tant qu’enfant de l’élément **InsertFunction**, **UpdateFunction**ou **DeleteFunction** , l’élément **ScalarProperty** mappe une propriété du modèle conceptuel à un paramètre de procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="2d13d-689">L’élément **ScalarProperty** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="2d13d-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-690">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-690">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-691">Les attributs qui s’appliquent à l’élément **ScalarProperty** varient en fonction du rôle de l’élément.</span><span class="sxs-lookup"><span data-stu-id="2d13d-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="2d13d-692">Le tableau suivant décrit les attributs qui s’appliquent lorsque l’élément **ScalarProperty** est utilisé pour mapper une propriété de modèle conceptuel à une colonne de la base de données :</span><span class="sxs-lookup"><span data-stu-id="2d13d-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="2d13d-693">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-693">Attribute Name</span></span> | <span data-ttu-id="2d13d-694">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-694">Is Required</span></span> | <span data-ttu-id="2d13d-695">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="2d13d-696">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-696">**Name**</span></span>       | <span data-ttu-id="2d13d-697">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-697">Yes</span></span>         | <span data-ttu-id="2d13d-698">Nom de la propriété de modèle conceptuel mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="2d13d-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-699">**ColumnName**</span></span> | <span data-ttu-id="2d13d-700">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-700">Yes</span></span>         | <span data-ttu-id="2d13d-701">Nom de la colonne de table mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="2d13d-702">Le tableau suivant décrit les attributs qui s’appliquent à l’élément **ScalarProperty** lorsqu’il est utilisé pour mapper une propriété de modèle conceptuel à un paramètre de procédure stockée :</span><span class="sxs-lookup"><span data-stu-id="2d13d-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="2d13d-703">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-703">Attribute Name</span></span>    | <span data-ttu-id="2d13d-704">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-704">Is Required</span></span> | <span data-ttu-id="2d13d-705">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-706">**Nom**</span><span class="sxs-lookup"><span data-stu-id="2d13d-706">**Name**</span></span>          | <span data-ttu-id="2d13d-707">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-707">Yes</span></span>         | <span data-ttu-id="2d13d-708">Nom de la propriété de modèle conceptuel mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="2d13d-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-709">**ParameterName**</span></span> | <span data-ttu-id="2d13d-710">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-710">Yes</span></span>         | <span data-ttu-id="2d13d-711">Nom du paramètre mappé.</span><span class="sxs-lookup"><span data-stu-id="2d13d-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="2d13d-712">**Version**</span><span class="sxs-lookup"><span data-stu-id="2d13d-712">**Version**</span></span>       | <span data-ttu-id="2d13d-713">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-713">No</span></span>          | <span data-ttu-id="2d13d-714">**Actuel** ou **original** selon que la valeur actuelle ou la valeur d’origine de la propriété doit être utilisée pour les contrôles d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="2d13d-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="2d13d-715">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-715">Example</span></span>

<span data-ttu-id="2d13d-716">L’exemple suivant montre l’élément **ScalarProperty** utilisé de deux manières :</span><span class="sxs-lookup"><span data-stu-id="2d13d-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="2d13d-717">Pour mapper les propriétés du type d’entité **Person** aux colonnes de la table **Person**.</span><span class="sxs-lookup"><span data-stu-id="2d13d-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="2d13d-718">Pour mapper les propriétés du type d’entité **Person** aux paramètres de la procédure stockée **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="2d13d-719">Les procédures stockées sont déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-719">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="2d13d-720">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-720">Example</span></span>

<span data-ttu-id="2d13d-721">L’exemple suivant montre l’élément **ScalarProperty** utilisé pour mapper les fonctions d’insertion et de suppression d’une association de modèle conceptuel à des procédures stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d13d-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="2d13d-722">Les procédures stockées sont déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-722">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="2d13d-723">UpdateFunction, élément (MSL)</span><span class="sxs-lookup"><span data-stu-id="2d13d-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="2d13d-724">L’élément **UpdateFunction** en Mapping Specification Language (MSL) mappe la fonction de mise à jour d’un type d’entité dans le modèle conceptuel à une procédure stockée dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="2d13d-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="2d13d-725">Les procédures stockées auxquelles des fonctions de modification sont mappées doivent être déclarées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="2d13d-726">Pour plus d’informations, consultez Function, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2d13d-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="2d13d-727">Si vous ne mappez pas les trois opérations d’insertion, de mise à jour ou de suppression d’un type d’entité à des procédures stockées, les opérations non mappées échouent si elles sont exécutées au moment de l’exécution et qu’une UpdateException est levée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="2d13d-728">L’élément **UpdateFunction** peut être un enfant de l’élément ModificationFunctionMapping et être appliqué à l’élément EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="2d13d-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="2d13d-729">L’élément **UpdateFunction** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="2d13d-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="2d13d-730">AssociationEnd (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="2d13d-731">ComplexProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="2d13d-732">ResultBinding (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="2d13d-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="2d13d-733">ScalarProperty (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="2d13d-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2d13d-734">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="2d13d-734">Applicable Attributes</span></span>

<span data-ttu-id="2d13d-735">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **UpdateFunction** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="2d13d-736">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="2d13d-736">Attribute Name</span></span>            | <span data-ttu-id="2d13d-737">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="2d13d-737">Is Required</span></span> | <span data-ttu-id="2d13d-738">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d13d-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2d13d-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="2d13d-739">**FunctionName**</span></span>          | <span data-ttu-id="2d13d-740">Oui</span><span class="sxs-lookup"><span data-stu-id="2d13d-740">Yes</span></span>         | <span data-ttu-id="2d13d-741">Nom qualifié par un espace de noms de la procédure stockée à laquelle la fonction de mise à jour est mappée.</span><span class="sxs-lookup"><span data-stu-id="2d13d-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="2d13d-742">La procédure stockée doit être déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="2d13d-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="2d13d-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="2d13d-744">Non</span><span class="sxs-lookup"><span data-stu-id="2d13d-744">No</span></span>          | <span data-ttu-id="2d13d-745">Nom du paramètre de sortie qui retourne le nombre de lignes affectées.</span><span class="sxs-lookup"><span data-stu-id="2d13d-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="2d13d-746">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d13d-746">Example</span></span>

<span data-ttu-id="2d13d-747">L’exemple suivant est basé sur le modèle School et montre l’élément **UpdateFunction** utilisé pour mapper la fonction de mise à jour du type d’entité **Person** à la procédure stockée **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="2d13d-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="2d13d-748">La procédure stockée **UpdatePerson** est déclarée dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="2d13d-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
