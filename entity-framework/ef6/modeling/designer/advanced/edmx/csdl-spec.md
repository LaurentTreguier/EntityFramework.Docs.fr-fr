---
title: Spécification CSDL-EF6
description: Spécification CSDL dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066354"
---
# <a name="csdl-specification"></a><span data-ttu-id="9f350-103">Spécification CSDL</span><span class="sxs-lookup"><span data-stu-id="9f350-103">CSDL Specification</span></span>
<span data-ttu-id="9f350-104">Le langage CSDL (Conceptual Schema Definition Language) est un langage basé sur XML qui décrit les entités, relations et fonctions qui composent un modèle conceptuel d'une application pilotée par les données.</span><span class="sxs-lookup"><span data-stu-id="9f350-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="9f350-105">Ce modèle conceptuel peut être utilisé par le Entity Framework ou WCF Data Services.</span><span class="sxs-lookup"><span data-stu-id="9f350-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="9f350-106">Les métadonnées qui sont décrites avec le langage CSDL sont utilisées par le Entity Framework pour mapper les entités et les relations définies dans un modèle conceptuel à une source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="9f350-107">Pour plus d’informations, consultez [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) et [spécification MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span><span class="sxs-lookup"><span data-stu-id="9f350-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="9f350-108">Le langage CSDL est l’implémentation de la Entity Framework du Entity Data Model.</span><span class="sxs-lookup"><span data-stu-id="9f350-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="9f350-109">Dans une application Entity Framework, les métadonnées du modèle conceptuel sont chargées à partir d’un fichier. CSDL (écrit en CSDL) dans une instance de System. Data. Metadata. Edm. EdmItemCollection et sont accessibles à l’aide des méthodes de la classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="9f350-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="9f350-110">Entity Framework utilise les métadonnées du modèle conceptuel pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques à la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="9f350-111">Le concepteur EF stocke les informations de modèle conceptuel dans un fichier. edmx au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="9f350-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="9f350-112">Au moment de la génération, le concepteur EF utilise les informations d’un fichier. edmx pour créer le fichier. csdl requis par Entity Framework au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="9f350-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="9f350-113">Les versions de CSDL sont différenciées par les espaces de noms XML.</span><span class="sxs-lookup"><span data-stu-id="9f350-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="9f350-114">Version CSDL</span><span class="sxs-lookup"><span data-stu-id="9f350-114">CSDL Version</span></span> | <span data-ttu-id="9f350-115">Espace de noms XML</span><span class="sxs-lookup"><span data-stu-id="9f350-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="9f350-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="9f350-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="9f350-117">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="9f350-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="9f350-118">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="9f350-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="9f350-119">Association, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-119">Association Element (CSDL)</span></span>

<span data-ttu-id="9f350-120">Un élément **Association** définit une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="9f350-121">Une association doit spécifier les types d'entités impliqués dans la relation et le nombre possible de types d'entités à chaque terminaison de la relation, appelé « multiplicité ».</span><span class="sxs-lookup"><span data-stu-id="9f350-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="9f350-122">La multiplicité d’une terminaison d’association peut avoir la valeur un (1), zéro ou un (0.. 1), ou plusieurs ( \* ).</span><span class="sxs-lookup"><span data-stu-id="9f350-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="9f350-123">Ces informations sont spécifiées dans deux éléments End enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="9f350-124">Il est possible d'accéder aux instances de type d'entité au niveau d'une terminaison d'une association via les propriétés de navigation ou les clés étrangères, si elles sont exposées sur un type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="9f350-125">Dans une application, une instance d'une association représente une association spécifique entre des instances de types d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="9f350-126">Les instances d'association sont regroupées logiquement dans un ensemble d'associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="9f350-127">Un élément **Association** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-128">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-129">End (exactement 2 éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="9f350-130">ReferentialConstraint (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="9f350-131">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-132">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-132">Applicable Attributes</span></span>

<span data-ttu-id="9f350-133">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="9f350-134">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-134">Attribute Name</span></span> | <span data-ttu-id="9f350-135">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-135">Is Required</span></span> | <span data-ttu-id="9f350-136">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="9f350-137">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-137">**Name**</span></span>       | <span data-ttu-id="9f350-138">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-138">Yes</span></span>         | <span data-ttu-id="9f350-139">Nom de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-140">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="9f350-141">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-142">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-143">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-143">Example</span></span>

<span data-ttu-id="9f350-144">L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** lorsque les clés étrangères n’ont pas été exposées sur les types d’entités **Customer** et **Order** .</span><span class="sxs-lookup"><span data-stu-id="9f350-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="9f350-145">Les valeurs de **multiplicité** pour chaque **terminaison** de l’Association indiquent que de nombreuses **commandes** peuvent être associées à un **client**, mais qu’un seul **client** peut être associé à une **commande**.</span><span class="sxs-lookup"><span data-stu-id="9f350-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="9f350-146">En outre, l’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext seront supprimées si le **client** est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="9f350-147">L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** lorsque des clés étrangères ont été exposées sur les types d’entités **Customer** et **Order** .</span><span class="sxs-lookup"><span data-stu-id="9f350-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="9f350-148">Avec les clés étrangères exposées, la relation entre les entités est gérée à l’aide d’un élément **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="9f350-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="9f350-149">Un élément AssociationSetMapping correspondant n'est pas nécessaire pour mapper cette association à la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="9f350-150">AssociationSet, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="9f350-151">L’élément **AssociationSet** en Conceptual Schema Definition Language (CSDL) est un conteneur logique pour les instances d’association du même type.</span><span class="sxs-lookup"><span data-stu-id="9f350-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="9f350-152">Un ensemble d'associations fournit une définition pour le regroupement d'instances d'association afin qu'elles puissent être mappées à une source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="9f350-153">L’élément **AssociationSet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-154">Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-155">End (exactement deux éléments requis) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="9f350-156">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="9f350-157">L’attribut **Association** spécifie le type d’association contenu dans un ensemble d’associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="9f350-158">Les jeux d’entités qui composent les terminaisons d’un ensemble d’associations sont spécifiés avec exactement deux éléments **finaux** enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-159">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-159">Applicable Attributes</span></span>

<span data-ttu-id="9f350-160">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="9f350-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="9f350-161">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-161">Attribute Name</span></span>  | <span data-ttu-id="9f350-162">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-162">Is Required</span></span> | <span data-ttu-id="9f350-163">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-164">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-164">**Name**</span></span>        | <span data-ttu-id="9f350-165">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-165">Yes</span></span>         | <span data-ttu-id="9f350-166">Nom du jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-166">The name of the entity set.</span></span> <span data-ttu-id="9f350-167">La valeur de l’attribut **Name** ne peut pas être la même que la valeur de l’attribut **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="9f350-168">**Association**</span><span class="sxs-lookup"><span data-stu-id="9f350-168">**Association**</span></span> | <span data-ttu-id="9f350-169">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-169">Yes</span></span>         | <span data-ttu-id="9f350-170">Nom qualifié complet de l'association dont l'ensemble d'associations contient des instances.</span><span class="sxs-lookup"><span data-stu-id="9f350-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="9f350-171">L'association doit être dans le même espace de noms que l'ensemble d'associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-172">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="9f350-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="9f350-173">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-174">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-175">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-175">Example</span></span>

<span data-ttu-id="9f350-176">L’exemple suivant montre un élément **EntityContainer** avec deux éléments **AssociationSet** :</span><span class="sxs-lookup"><span data-stu-id="9f350-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="9f350-177">Élément CollectionType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="9f350-178">L’élément **CollectionType** en Conceptual Schema Definition Language (CSDL) spécifie qu’un paramètre de fonction ou un type de retour de fonction est une collection.</span><span class="sxs-lookup"><span data-stu-id="9f350-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="9f350-179">L’élément **CollectionType** peut être un enfant de l’élément parameter ou de l’élément ReturnType (Function).</span><span class="sxs-lookup"><span data-stu-id="9f350-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="9f350-180">Le type de collection peut être spécifié à l’aide de l’attribut **type** ou de l’un des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="9f350-181">**CollectionType ;**</span><span class="sxs-lookup"><span data-stu-id="9f350-181">**CollectionType**</span></span>
-   <span data-ttu-id="9f350-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9f350-182">ReferenceType</span></span>
-   <span data-ttu-id="9f350-183">RowType</span><span class="sxs-lookup"><span data-stu-id="9f350-183">RowType</span></span>
-   <span data-ttu-id="9f350-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="9f350-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-185">Un modèle ne sera pas validé si le type d’une collection est spécifié avec l’attribut **type** et un élément enfant.</span><span class="sxs-lookup"><span data-stu-id="9f350-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-186">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-186">Applicable Attributes</span></span>

<span data-ttu-id="9f350-187">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9f350-188">Notez que les attributs **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**et **collation** sont uniquement applicables aux collections de **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="9f350-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="9f350-189">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-189">Attribute Name</span></span>                                                          | <span data-ttu-id="9f350-190">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-190">Is Required</span></span> | <span data-ttu-id="9f350-191">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-192">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-192">**Type**</span></span>                                                                | <span data-ttu-id="9f350-193">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-193">No</span></span>          | <span data-ttu-id="9f350-194">Type de la collection.</span><span class="sxs-lookup"><span data-stu-id="9f350-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9f350-195">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-195">**Nullable**</span></span>                                                            | <span data-ttu-id="9f350-196">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-196">No</span></span>          | <span data-ttu-id="9f350-197">**True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null.</span><span class="sxs-lookup"><span data-stu-id="9f350-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="9f350-198">> dans le CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="9f350-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="9f350-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9f350-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="9f350-200">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-200">No</span></span>          | <span data-ttu-id="9f350-201">Valeur par défaut de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="9f350-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="9f350-203">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-203">No</span></span>          | <span data-ttu-id="9f350-204">Longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="9f350-205">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="9f350-206">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-206">No</span></span>          | <span data-ttu-id="9f350-207">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="9f350-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="9f350-208">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-208">**Precision**</span></span>                                                           | <span data-ttu-id="9f350-209">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-209">No</span></span>          | <span data-ttu-id="9f350-210">Précision de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="9f350-211">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-211">**Scale**</span></span>                                                               | <span data-ttu-id="9f350-212">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-212">No</span></span>          | <span data-ttu-id="9f350-213">Échelle de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-214">**SRID**</span></span>                                                                | <span data-ttu-id="9f350-215">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-215">No</span></span>          | <span data-ttu-id="9f350-216">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-217">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="9f350-218">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="9f350-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="9f350-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-219">**Unicode**</span></span>                                                             | <span data-ttu-id="9f350-220">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-220">No</span></span>          | <span data-ttu-id="9f350-221">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="9f350-222">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-222">**Collation**</span></span>                                                           | <span data-ttu-id="9f350-223">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-223">No</span></span>          | <span data-ttu-id="9f350-224">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="9f350-225">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9f350-226">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-227">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-228">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-228">Example</span></span>

<span data-ttu-id="9f350-229">L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de types d’entité **Person** (comme spécifié avec l’attribut **ElementType** ).</span><span class="sxs-lookup"><span data-stu-id="9f350-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="9f350-230">L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="9f350-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="9f350-231">L’exemple suivant montre une fonction définie par modèle qui utilise l’élément **CollectionType** pour spécifier que la fonction accepte comme paramètre une collection de types d’entités **Department** .</span><span class="sxs-lookup"><span data-stu-id="9f350-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="9f350-232">ComplexType, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="9f350-233">Un élément **complexType** définit une structure de données composée de propriétés **type EDMSimpleType** ou d’autres types complexes.</span><span class="sxs-lookup"><span data-stu-id="9f350-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="9f350-234">Un type complexe peut être une propriété d'un type d'entité ou d'un autre type complexe.</span><span class="sxs-lookup"><span data-stu-id="9f350-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="9f350-235">Un type complexe est semblable à un type d'entité du fait qu'un type complexe définit des données.</span><span class="sxs-lookup"><span data-stu-id="9f350-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="9f350-236">Toutefois, il existe des différences clés entre les types complexes et les types d'entités :</span><span class="sxs-lookup"><span data-stu-id="9f350-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="9f350-237">Les types complexes n'ont pas d'identité (ni de clés), par conséquent, ils ne peuvent pas exister indépendamment.</span><span class="sxs-lookup"><span data-stu-id="9f350-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="9f350-238">Les types complexes peuvent uniquement exister en tant que propriétés de types d'entités ou d'autres types complexes.</span><span class="sxs-lookup"><span data-stu-id="9f350-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="9f350-239">Les types complexes ne peuvent pas participer à des associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="9f350-240">Aucune terminaison d'association ne peut être un type complexe ; par conséquent, il n'est pas possible de définir des propriétés de navigation pour des types complexes.</span><span class="sxs-lookup"><span data-stu-id="9f350-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="9f350-241">Une propriété de type complexe ne peut pas avoir de valeur NULL, bien que les propriétés scalaires d'un type complexe puissent chacune être définie sur NULL.</span><span class="sxs-lookup"><span data-stu-id="9f350-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="9f350-242">Un élément **complexType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-243">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-244">Property (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="9f350-245">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="9f350-246">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **complexType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="9f350-247">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="9f350-248">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-248">Is Required</span></span> | <span data-ttu-id="9f350-249">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-250">Nom</span><span class="sxs-lookup"><span data-stu-id="9f350-250">Name</span></span>                                                                                                           | <span data-ttu-id="9f350-251">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-251">Yes</span></span>         | <span data-ttu-id="9f350-252">Nom du type complexe.</span><span class="sxs-lookup"><span data-stu-id="9f350-252">The name of the complex type.</span></span> <span data-ttu-id="9f350-253">Le nom d'un type complexe ne peut pas être identique au nom d'un autre type complexe, d'un type d'entité ou d'une association qui figure dans l'étendue du modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="9f350-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="9f350-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="9f350-255">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-255">No</span></span>          | <span data-ttu-id="9f350-256">Nom d'un autre type complexe qui est le type de base du type complexe en cours de définition.</span><span class="sxs-lookup"><span data-stu-id="9f350-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="9f350-257">> cet attribut n’est pas applicable dans CSDL v1.</span><span class="sxs-lookup"><span data-stu-id="9f350-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="9f350-258">L'héritage pour les types complexes n'est pas pris en charge dans cette version.</span><span class="sxs-lookup"><span data-stu-id="9f350-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="9f350-259">Résumé</span><span class="sxs-lookup"><span data-stu-id="9f350-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="9f350-260">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-260">No</span></span>          | <span data-ttu-id="9f350-261">**True** ou **false** (valeur par défaut), selon que le type complexe est un type abstrait.</span><span class="sxs-lookup"><span data-stu-id="9f350-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="9f350-262">> cet attribut n’est pas applicable dans CSDL v1.</span><span class="sxs-lookup"><span data-stu-id="9f350-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="9f350-263">Les types complexes dans cette version ne peuvent pas être des types abstraits.</span><span class="sxs-lookup"><span data-stu-id="9f350-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="9f350-264">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **complexType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="9f350-265">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-266">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-267">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-267">Example</span></span>

<span data-ttu-id="9f350-268">L’exemple suivant montre un type complexe, **Address**, avec les **EdmSimpleType** propriétés type EDMSimpleType **StreetAddress**, **City**, **StateOrProvince**, **Country**et **PostalCode**.</span><span class="sxs-lookup"><span data-stu-id="9f350-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="9f350-269">Pour définir l' **adresse** de type complexe (ci-dessus) en tant que propriété d’un type d’entité, vous devez déclarer le type de propriété dans la définition du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="9f350-270">L’exemple suivant illustre la propriété **Address** en tant que type complexe sur un type d’entité (**éditeur**) :</span><span class="sxs-lookup"><span data-stu-id="9f350-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="9f350-271">DefiningExpression, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="9f350-272">L’élément **DefiningExpression** en Conceptual Schema Definition Language (CSDL) contient une expression Entity SQL qui définit une fonction dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="9f350-273">À des fins de validation, un élément **DefiningExpression** peut contenir du contenu arbitraire.</span><span class="sxs-lookup"><span data-stu-id="9f350-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="9f350-274">Toutefois, Entity Framework lèvera une exception au moment de l’exécution si un élément **DefiningExpression** ne contient pas d’Entity SQL valide.</span><span class="sxs-lookup"><span data-stu-id="9f350-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-275">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-275">Applicable Attributes</span></span>

<span data-ttu-id="9f350-276">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **DefiningExpression** .</span><span class="sxs-lookup"><span data-stu-id="9f350-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="9f350-277">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-278">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-279">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-279">Example</span></span>

<span data-ttu-id="9f350-280">L’exemple suivant utilise un élément **DefiningExpression** pour définir une fonction qui retourne le nombre d’années écoulées depuis la publication d’un livre.</span><span class="sxs-lookup"><span data-stu-id="9f350-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="9f350-281">Le contenu de l’élément **DefiningExpression** est écrit en Entity SQL.</span><span class="sxs-lookup"><span data-stu-id="9f350-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="9f350-282">Dependent, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="9f350-283">L’élément **dépendant** en Conceptual Schema Definition Language (CSDL) est un élément enfant de l’élément ReferentialConstraint et définit la terminaison dépendante d’une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="9f350-284">Un élément **ReferentialConstraint** définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="9f350-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9f350-285">De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9f350-286">Le type d’entité référencé est appelé *terminaison principale* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9f350-287">Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="9f350-288">Les éléments **PropertyRef** sont utilisés pour spécifier les clés qui référencent la terminaison principale.</span><span class="sxs-lookup"><span data-stu-id="9f350-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="9f350-289">L’élément **dépendant** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-290">PropertyRef (un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9f350-291">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-292">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-292">Applicable Attributes</span></span>

<span data-ttu-id="9f350-293">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="9f350-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="9f350-294">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-294">Attribute Name</span></span> | <span data-ttu-id="9f350-295">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-295">Is Required</span></span> | <span data-ttu-id="9f350-296">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="9f350-297">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="9f350-297">**Role**</span></span>       | <span data-ttu-id="9f350-298">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-298">Yes</span></span>         | <span data-ttu-id="9f350-299">Nom du type d'entité au niveau de la terminaison dépendante de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-300">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="9f350-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="9f350-301">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-302">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-303">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-303">Example</span></span>

<span data-ttu-id="9f350-304">L’exemple suivant montre un élément **ReferentialConstraint** utilisé dans le cadre de la définition de l’Association **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="9f350-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="9f350-305">La propriété **PublisherId** du type **d’entité Book** compose la terminaison dépendante de la contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="9f350-306">Documentation, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="9f350-307">L’élément **documentation** en Conceptual Schema Definition Language (CSDL) peut être utilisé pour fournir des informations sur un objet défini dans un élément parent.</span><span class="sxs-lookup"><span data-stu-id="9f350-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="9f350-308">Dans un fichier. edmx, lorsque l’élément **documentation** est un enfant d’un élément qui apparaît en tant qu’objet sur l’aire de conception du concepteur EF (tel qu’une entité, une association ou une propriété), le contenu de l’élément **documentation** s’affiche dans la fenêtre **Propriétés** de Visual Studio pour l’objet.</span><span class="sxs-lookup"><span data-stu-id="9f350-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="9f350-309">L’élément **documentation** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-310">**Summary**: brève description de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="9f350-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="9f350-311">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="9f350-311">(zero or one element)</span></span>
-   <span data-ttu-id="9f350-312">**LongDescription**: description complète de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="9f350-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="9f350-313">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="9f350-313">(zero or one element)</span></span>
-   <span data-ttu-id="9f350-314">éléments d'annotation</span><span class="sxs-lookup"><span data-stu-id="9f350-314">Annotation elements.</span></span> <span data-ttu-id="9f350-315">(zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-316">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-316">Applicable Attributes</span></span>

<span data-ttu-id="9f350-317">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **documentation** .</span><span class="sxs-lookup"><span data-stu-id="9f350-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="9f350-318">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-319">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-320">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-320">Example</span></span>

<span data-ttu-id="9f350-321">L’exemple suivant montre l’élément de **documentation** en tant qu’élément enfant d’un élément EntityType.</span><span class="sxs-lookup"><span data-stu-id="9f350-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="9f350-322">Si l’extrait de code ci-dessous se trouve dans le contenu CSDL d’un fichier. edmx, le contenu des éléments **Summary** et **LongDescription** apparaît dans la fenêtre **Propriétés** de Visual Studio lorsque vous cliquez sur le `Customer` type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="9f350-323">End, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-323">End Element (CSDL)</span></span>

<span data-ttu-id="9f350-324">L’élément **end** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément Association ou de l’élément AssociationSet.</span><span class="sxs-lookup"><span data-stu-id="9f350-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="9f350-325">Dans chaque cas, le rôle de l’élément de **fin** est différent et les attributs applicables sont différents.</span><span class="sxs-lookup"><span data-stu-id="9f350-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="9f350-326">Élément End comme enfant de l'élément Association</span><span class="sxs-lookup"><span data-stu-id="9f350-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="9f350-327">Un élément **end** (en tant qu’enfant de l’élément **Association** ) identifie le type d’entité à une terminaison d’une association et le nombre d’instances de type d’entité qui peuvent exister à cette terminaison d’une association.</span><span class="sxs-lookup"><span data-stu-id="9f350-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="9f350-328">Les terminaisons d'association sont définies dans le cadre d'une association ; une association doit avoir exactement deux terminaisons d'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="9f350-329">Il est possible d'accéder aux instances de type d'entité au niveau de la terminaison d'une association via les propriétés de navigation ou les clés étrangères si elles sont exposées sur un type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="9f350-330">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-331">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-332">OnDelete (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="9f350-333">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-334">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-334">Applicable Attributes</span></span>

<span data-ttu-id="9f350-335">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **final** lorsqu’il est l’enfant d’un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="9f350-336">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-336">Attribute Name</span></span>   | <span data-ttu-id="9f350-337">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-337">Is Required</span></span> | <span data-ttu-id="9f350-338">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-339">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-339">**Type**</span></span>         | <span data-ttu-id="9f350-340">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-340">Yes</span></span>         | <span data-ttu-id="9f350-341">Nom du type d'entité au niveau de la terminaison de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="9f350-342">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="9f350-342">**Role**</span></span>         | <span data-ttu-id="9f350-343">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-343">No</span></span>          | <span data-ttu-id="9f350-344">Nom de la terminaison de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-344">A name for the association end.</span></span> <span data-ttu-id="9f350-345">Si aucun nom n'est fourni, le nom du type d'entité au niveau de la terminaison de l'association sera utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f350-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="9f350-346">**Multiplicité**</span><span class="sxs-lookup"><span data-stu-id="9f350-346">**Multiplicity**</span></span> | <span data-ttu-id="9f350-347">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-347">Yes</span></span>         | <span data-ttu-id="9f350-348">**1**, **0.. 1**, ou **\*** selon le nombre d’instances de type d’entité qui peuvent être à la fin de l’Association.</span><span class="sxs-lookup"><span data-stu-id="9f350-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="9f350-349">**1** indique qu’il existe exactement une instance de type d’entité au niveau de la terminaison d’association.</span><span class="sxs-lookup"><span data-stu-id="9f350-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="9f350-350">**0.. 1** indique que zéro ou une instance de type d’entité existe au niveau de la terminaison d’association.</span><span class="sxs-lookup"><span data-stu-id="9f350-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="9f350-351">**\*** indique qu’il existe zéro, une ou plusieurs instances de type d’entité au niveau de la terminaison d’association.</span><span class="sxs-lookup"><span data-stu-id="9f350-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-352">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="9f350-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="9f350-353">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-354">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-355">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-355">Example</span></span>

<span data-ttu-id="9f350-356">L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** .</span><span class="sxs-lookup"><span data-stu-id="9f350-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="9f350-357">Les valeurs de **multiplicité** pour chaque **terminaison** de l’Association indiquent que de nombreuses **commandes** peuvent être associées à un **client**, mais qu’un seul **client** peut être associé à une **commande**.</span><span class="sxs-lookup"><span data-stu-id="9f350-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="9f350-358">En outre, l’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext sont supprimées si le **client** est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="9f350-359">Élément End comme enfant de l'élément AssociationSet</span><span class="sxs-lookup"><span data-stu-id="9f350-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="9f350-360">L’élément **end** spécifie une extrémité d’un ensemble d’associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="9f350-361">L’élément **AssociationSet** doit contenir deux éléments **end** .</span><span class="sxs-lookup"><span data-stu-id="9f350-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="9f350-362">Les informations contenues dans un élément **end** sont utilisées dans le mappage d’un ensemble d’associations à une source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="9f350-363">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-364">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-365">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-366">Les éléments d'annotation doivent figurer après tous les autres éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="9f350-367">Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9f350-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-368">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-368">Applicable Attributes</span></span>

<span data-ttu-id="9f350-369">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **end** lorsqu’il est l’enfant d’un élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="9f350-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="9f350-370">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-370">Attribute Name</span></span> | <span data-ttu-id="9f350-371">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-371">Is Required</span></span> | <span data-ttu-id="9f350-372">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-373">**EntitySet ;**</span><span class="sxs-lookup"><span data-stu-id="9f350-373">**EntitySet**</span></span>  | <span data-ttu-id="9f350-374">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-374">Yes</span></span>         | <span data-ttu-id="9f350-375">Nom de l’élément **EntitySet** qui définit une extrémité de l’élément **AssociationSet** parent.</span><span class="sxs-lookup"><span data-stu-id="9f350-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="9f350-376">L’élément **EntitySet** doit être défini dans le même conteneur d’entités que l’élément **AssociationSet** parent.</span><span class="sxs-lookup"><span data-stu-id="9f350-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="9f350-377">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="9f350-377">**Role**</span></span>       | <span data-ttu-id="9f350-378">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-378">No</span></span>          | <span data-ttu-id="9f350-379">Nom de la terminaison de l'ensemble d'associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-379">The name of the association set end.</span></span> <span data-ttu-id="9f350-380">Si l’attribut **role** n’est pas utilisé, le nom de la terminaison de l’ensemble d’associations sera le nom du jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9f350-381">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="9f350-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="9f350-382">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-383">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-384">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-384">Example</span></span>

<span data-ttu-id="9f350-385">L’exemple suivant montre un élément **EntityContainer** avec deux éléments **AssociationSet** , chacun avec deux éléments **end** :</span><span class="sxs-lookup"><span data-stu-id="9f350-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="9f350-386">EntityContainer, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="9f350-387">L’élément **EntityContainer** en Conceptual Schema Definition Language (CSDL) est un conteneur logique pour les jeux d’entités, les ensembles d’associations et les importations de fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="9f350-388">Un conteneur d'entités de modèle conceptuel est mappé à un conteneur d'entités de modèle de stockage via l'élément EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="9f350-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="9f350-389">Un conteneur d'entités de modèle de stockage décrit la structure de la base de données : les jeux d'entités décrivent les tables, les ensembles d'associations décrivent les contraintes de clé étrangère et les importations de fonction décrivent les procédures stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="9f350-390">Un élément **EntityContainer** peut avoir zéro ou un élément documentation.</span><span class="sxs-lookup"><span data-stu-id="9f350-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="9f350-391">Si un élément de **documentation** est présent, il doit précéder tous les éléments **EntitySet**, **AssociationSet**et **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="9f350-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="9f350-392">Un élément **EntityContainer** peut avoir zéro ou plusieurs des éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-393">EntitySet ;</span><span class="sxs-lookup"><span data-stu-id="9f350-393">EntitySet</span></span>
-   <span data-ttu-id="9f350-394">AssociationSet ;</span><span class="sxs-lookup"><span data-stu-id="9f350-394">AssociationSet</span></span>
-   <span data-ttu-id="9f350-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="9f350-395">FunctionImport</span></span>
-   <span data-ttu-id="9f350-396">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="9f350-396">Annotation elements</span></span>

<span data-ttu-id="9f350-397">Vous pouvez étendre un élément **EntityContainer** pour inclure le contenu d’un autre **EntityContainer** qui se trouve dans le même espace de noms.</span><span class="sxs-lookup"><span data-stu-id="9f350-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="9f350-398">Pour inclure le contenu d’un autre **EntityContainer**, dans l’élément **EntityContainer** de référence, définissez la valeur de l’attribut **extends** sur le nom de l’élément **EntityContainer** que vous souhaitez inclure.</span><span class="sxs-lookup"><span data-stu-id="9f350-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="9f350-399">Tous les éléments enfants de l’élément **EntityContainer** inclus sont traités comme des éléments enfants de l’élément **EntityContainer** de référence.</span><span class="sxs-lookup"><span data-stu-id="9f350-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-400">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-400">Applicable Attributes</span></span>

<span data-ttu-id="9f350-401">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **using** .</span><span class="sxs-lookup"><span data-stu-id="9f350-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="9f350-402">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-402">Attribute Name</span></span> | <span data-ttu-id="9f350-403">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-403">Is Required</span></span> | <span data-ttu-id="9f350-404">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="9f350-405">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-405">**Name**</span></span>       | <span data-ttu-id="9f350-406">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-406">Yes</span></span>         | <span data-ttu-id="9f350-407">Nom du conteneur d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="9f350-408">**Dure**</span><span class="sxs-lookup"><span data-stu-id="9f350-408">**Extends**</span></span>    | <span data-ttu-id="9f350-409">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-409">No</span></span>          | <span data-ttu-id="9f350-410">Le nom d'un autre conteneur d'entités au sein du même espace de noms.</span><span class="sxs-lookup"><span data-stu-id="9f350-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-411">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="9f350-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="9f350-412">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-413">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-414">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-414">Example</span></span>

<span data-ttu-id="9f350-415">L’exemple suivant montre un élément **EntityContainer** qui définit trois jeux d’entités et deux ensembles d’associations.</span><span class="sxs-lookup"><span data-stu-id="9f350-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="9f350-416">EntitySet, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="9f350-417">L’élément **EntitySet** dans Conceptual Schema Definition Language est un conteneur logique pour les instances d’un type d’entité et les instances de tout type dérivé de ce type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="9f350-418">La relation entre un type d'entité et un jeu d'entités est analogue à la relation entre une ligne et une table dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="9f350-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="9f350-419">Comme une ligne, un type d'entité définit un jeu de données connexes et, comme une table, un jeu d'entités contient des instances de cette définition.</span><span class="sxs-lookup"><span data-stu-id="9f350-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="9f350-420">Un jeu d'entités fournit une construction pour le regroupement d'instances du type d'entité, afin qu'elles puissent être mappées aux structures de données associées dans une source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="9f350-421">Plusieurs jeux d'entités peuvent être définis pour un type d'entité particulier.</span><span class="sxs-lookup"><span data-stu-id="9f350-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-422">Le concepteur EF ne prend pas en charge les modèles conceptuels qui contiennent plusieurs jeux d’entités par type.</span><span class="sxs-lookup"><span data-stu-id="9f350-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="9f350-423">L’élément **EntitySet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-424">élément Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-425">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-426">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-426">Applicable Attributes</span></span>

<span data-ttu-id="9f350-427">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="9f350-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="9f350-428">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-428">Attribute Name</span></span> | <span data-ttu-id="9f350-429">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-429">Is Required</span></span> | <span data-ttu-id="9f350-430">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-431">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-431">**Name**</span></span>       | <span data-ttu-id="9f350-432">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-432">Yes</span></span>         | <span data-ttu-id="9f350-433">Nom du jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="9f350-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="9f350-434">**EntityType**</span></span> | <span data-ttu-id="9f350-435">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-435">Yes</span></span>         | <span data-ttu-id="9f350-436">Nom qualifié complet du type d'entité pour lequel le jeu d'entités contient des instances.</span><span class="sxs-lookup"><span data-stu-id="9f350-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-437">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="9f350-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="9f350-438">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-439">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-440">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-440">Example</span></span>

<span data-ttu-id="9f350-441">L’exemple suivant montre un élément **EntityContainer** avec trois éléments **EntitySet** :</span><span class="sxs-lookup"><span data-stu-id="9f350-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="9f350-442">Il est possible de définir des jeux d'entités multiples par type (MEST).</span><span class="sxs-lookup"><span data-stu-id="9f350-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="9f350-443">L’exemple suivant définit un conteneur d’entités avec deux jeux d’entités pour le type **d’entité Book** :</span><span class="sxs-lookup"><span data-stu-id="9f350-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="9f350-444">EntityType, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="9f350-445">L’élément **EntityType** représente la structure d’un concept de niveau supérieur, tel qu’un client ou une commande, dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="9f350-446">Un type d'entité est un modèle pour des instances de types d'entités dans une application.</span><span class="sxs-lookup"><span data-stu-id="9f350-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="9f350-447">Chaque modèle contient les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f350-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="9f350-448">Nom unique.</span><span class="sxs-lookup"><span data-stu-id="9f350-448">A unique name.</span></span> <span data-ttu-id="9f350-449">(Obligatoire.)</span><span class="sxs-lookup"><span data-stu-id="9f350-449">(Required.)</span></span>
-   <span data-ttu-id="9f350-450">Clé d'entité définie par une ou plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="9f350-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="9f350-451">(Obligatoire.)</span><span class="sxs-lookup"><span data-stu-id="9f350-451">(Required.)</span></span>
-   <span data-ttu-id="9f350-452">Propriétés pour contenir des données.</span><span class="sxs-lookup"><span data-stu-id="9f350-452">Properties for containing data.</span></span> <span data-ttu-id="9f350-453">(Facultatif.)</span><span class="sxs-lookup"><span data-stu-id="9f350-453">(Optional.)</span></span>
-   <span data-ttu-id="9f350-454">Propriétés de navigation qui permettent de naviguer d'une terminaison d'une association à l'autre terminaison.</span><span class="sxs-lookup"><span data-stu-id="9f350-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="9f350-455">(Facultatif.)</span><span class="sxs-lookup"><span data-stu-id="9f350-455">(Optional.)</span></span>

<span data-ttu-id="9f350-456">Dans une application, une instance d'un type d'entité représente un objet spécifique (tel qu'un client ou une commande spécifique).</span><span class="sxs-lookup"><span data-stu-id="9f350-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="9f350-457">Chaque instance d'un type d'entité doit avoir une clé d'entité unique dans un jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="9f350-458">Deux instances de type d'entité sont considérées égales seulement si elles sont du même type et si les valeurs de leurs clés d'entité sont identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="9f350-459">Un élément **EntityType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-460">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-461">Key (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="9f350-462">Property (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="9f350-463">NavigationProperty (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="9f350-464">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-465">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-465">Applicable Attributes</span></span>

<span data-ttu-id="9f350-466">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="9f350-467">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="9f350-468">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-468">Is Required</span></span> | <span data-ttu-id="9f350-469">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-470">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="9f350-471">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-471">Yes</span></span>         | <span data-ttu-id="9f350-472">Le nom du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="9f350-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="9f350-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="9f350-474">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-474">No</span></span>          | <span data-ttu-id="9f350-475">Le nom d’un autre type d’entité qui est le type de base du type d’entité défini.</span><span class="sxs-lookup"><span data-stu-id="9f350-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="9f350-476">**Abstraction**</span><span class="sxs-lookup"><span data-stu-id="9f350-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="9f350-477">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-477">No</span></span>          | <span data-ttu-id="9f350-478">**True** ou **false**, selon que le type d’entité est un type abstrait ou non.</span><span class="sxs-lookup"><span data-stu-id="9f350-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="9f350-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="9f350-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="9f350-480">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-480">No</span></span>          | <span data-ttu-id="9f350-481">**True** ou **false** selon que le type d’entité est un type d’entité ouvert.</span><span class="sxs-lookup"><span data-stu-id="9f350-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="9f350-482">> l’attribut **OpenType** s’applique uniquement aux types d’entité définis dans les modèles conceptuels utilisés avec ADO.NET Data Services.</span><span class="sxs-lookup"><span data-stu-id="9f350-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="9f350-483">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="9f350-484">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-485">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-486">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-486">Example</span></span>

<span data-ttu-id="9f350-487">L’exemple suivant montre un élément **EntityType** avec trois éléments de **propriété** et deux éléments **NavigationProperty** :</span><span class="sxs-lookup"><span data-stu-id="9f350-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="9f350-488">EnumType, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="9f350-489">L’élément **enumType** représente un type énuméré.</span><span class="sxs-lookup"><span data-stu-id="9f350-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="9f350-490">Un élément **enumType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-491">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-492">Membre (zéro, un ou plusieurs éléments)</span><span class="sxs-lookup"><span data-stu-id="9f350-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="9f350-493">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-494">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-494">Applicable Attributes</span></span>

<span data-ttu-id="9f350-495">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **enumType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="9f350-496">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-496">Attribute Name</span></span>     | <span data-ttu-id="9f350-497">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-497">Is Required</span></span> | <span data-ttu-id="9f350-498">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-499">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-499">**Name**</span></span>           | <span data-ttu-id="9f350-500">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-500">Yes</span></span>         | <span data-ttu-id="9f350-501">Le nom du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="9f350-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="9f350-502">**IsFlags**</span></span>        | <span data-ttu-id="9f350-503">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-503">No</span></span>          | <span data-ttu-id="9f350-504">**True** ou **false**, selon que le type enum peut être utilisé comme un ensemble d’indicateurs.</span><span class="sxs-lookup"><span data-stu-id="9f350-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="9f350-505">La valeur par défaut est **false.**</span><span class="sxs-lookup"><span data-stu-id="9f350-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="9f350-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="9f350-506">**UnderlyingType**</span></span> | <span data-ttu-id="9f350-507">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-507">No</span></span>          | <span data-ttu-id="9f350-508">**Edm. Byte**, **Edm. Int16**, **Edm. Int32**, **Edm. Int64** ou **Edm. SByte** définissant la plage de valeurs du type.</span><span class="sxs-lookup"><span data-stu-id="9f350-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="9f350-509">Le type sous-jacent par défaut des éléments d’énumération est **Edm. Int32.**.</span><span class="sxs-lookup"><span data-stu-id="9f350-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-510">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **enumType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="9f350-511">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-512">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-513">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-513">Example</span></span>

<span data-ttu-id="9f350-514">L’exemple suivant montre un élément **enumType** avec trois éléments **membres** :</span><span class="sxs-lookup"><span data-stu-id="9f350-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="9f350-515">Function, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-515">Function Element (CSDL)</span></span>

<span data-ttu-id="9f350-516">L’élément de **fonction** en Conceptual Schema Definition Language (CSDL) est utilisé pour définir ou déclarer des fonctions dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="9f350-517">Une fonction est définie à l'aide d'un élément DefiningExpression.</span><span class="sxs-lookup"><span data-stu-id="9f350-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="9f350-518">Un élément **Function** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-519">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-520">Parameter (zéro, un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="9f350-521">DefiningExpression (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="9f350-522">ReturnType (Function) (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="9f350-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="9f350-523">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="9f350-524">Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** (Function) ou **ReturnType** (voir ci-dessous), mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="9f350-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="9f350-525">Les types de retour possibles correspondent à tout type EdmSimpleType, type d'entité, type complexe, type de ligne ou type REF (ou à une collection de l'un de ces types).</span><span class="sxs-lookup"><span data-stu-id="9f350-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-526">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-526">Applicable Attributes</span></span>

<span data-ttu-id="9f350-527">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Function** .</span><span class="sxs-lookup"><span data-stu-id="9f350-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="9f350-528">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-528">Attribute Name</span></span> | <span data-ttu-id="9f350-529">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-529">Is Required</span></span> | <span data-ttu-id="9f350-530">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="9f350-531">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-531">**Name**</span></span>       | <span data-ttu-id="9f350-532">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-532">Yes</span></span>         | <span data-ttu-id="9f350-533">Nom de la fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-533">The name of the function.</span></span>          |
| <span data-ttu-id="9f350-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9f350-534">**ReturnType**</span></span> | <span data-ttu-id="9f350-535">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-535">No</span></span>          | <span data-ttu-id="9f350-536">Type retourné par la fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-537">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fonction** .</span><span class="sxs-lookup"><span data-stu-id="9f350-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="9f350-538">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-539">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-540">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-540">Example</span></span>

<span data-ttu-id="9f350-541">L’exemple suivant utilise un élément **Function** pour définir une fonction qui retourne le nombre d’années écoulées depuis l’embauche d’un formateur.</span><span class="sxs-lookup"><span data-stu-id="9f350-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="9f350-542">FunctionImport, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="9f350-543">L’élément **FunctionImport** en Conceptual Schema Definition Language (CSDL) représente une fonction qui est définie dans la source de données, mais qui est disponible pour les objets via le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="9f350-544">Par exemple, un élément Function dans le modèle de stockage peut être utilisé pour représenter une procédure stockée dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="9f350-545">Un élément **FunctionImport** du modèle conceptuel représente la fonction correspondante dans une Entity Framework application et est mappée à la fonction de modèle de stockage à l’aide de l’élément FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="9f350-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="9f350-546">Lorsque la fonction est appelée dans l'application, la procédure stockée correspondante est exécutée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="9f350-547">L’élément **FunctionImport** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-548">Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-549">Parameter (zéro, un ou plusieurs éléments autorisés) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="9f350-550">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="9f350-551">ReturnType (FunctionImport) (zéro, un ou plusieurs éléments autorisés)</span><span class="sxs-lookup"><span data-stu-id="9f350-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="9f350-552">Un élément de **paramètre** doit être défini pour chaque paramètre que la fonction accepte.</span><span class="sxs-lookup"><span data-stu-id="9f350-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="9f350-553">Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** (FunctionImport) ou **ReturnType** (voir ci-dessous), mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="9f350-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="9f350-554">La valeur du type de retour doit être une collection de type EDMSimpleType, d’EntityType ou de ComplexType.</span><span class="sxs-lookup"><span data-stu-id="9f350-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-555">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-555">Applicable Attributes</span></span>

<span data-ttu-id="9f350-556">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="9f350-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="9f350-557">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-557">Attribute Name</span></span>   | <span data-ttu-id="9f350-558">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-558">Is Required</span></span> | <span data-ttu-id="9f350-559">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-560">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-560">**Name**</span></span>         | <span data-ttu-id="9f350-561">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-561">Yes</span></span>         | <span data-ttu-id="9f350-562">Nom de la fonction importée.</span><span class="sxs-lookup"><span data-stu-id="9f350-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="9f350-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9f350-563">**ReturnType**</span></span>   | <span data-ttu-id="9f350-564">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-564">No</span></span>          | <span data-ttu-id="9f350-565">Type retourné par la fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-565">The type that the function returns.</span></span> <span data-ttu-id="9f350-566">N’utilisez pas cet attribut si la fonction ne renvoie pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="9f350-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="9f350-567">Dans le cas contraire, la valeur doit être une collection de ComplexType, EntityType ou type EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="9f350-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="9f350-568">**EntitySet ;**</span><span class="sxs-lookup"><span data-stu-id="9f350-568">**EntitySet**</span></span>    | <span data-ttu-id="9f350-569">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-569">No</span></span>          | <span data-ttu-id="9f350-570">Si la fonction retourne une collection de types d’entités, la valeur de l' **EntitySet** doit être le jeu d’entités auquel la collection appartient.</span><span class="sxs-lookup"><span data-stu-id="9f350-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="9f350-571">Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f350-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="9f350-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="9f350-572">**IsComposable**</span></span> | <span data-ttu-id="9f350-573">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-573">No</span></span>          | <span data-ttu-id="9f350-574">Si la valeur est définie sur true, la fonction est composable (fonction table) et peut être utilisée dans une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="9f350-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="9f350-575">La valeur par défaut est **false**.</span><span class="sxs-lookup"><span data-stu-id="9f350-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="9f350-576">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="9f350-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="9f350-577">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-578">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-579">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-579">Example</span></span>

<span data-ttu-id="9f350-580">L’exemple suivant montre un élément **FunctionImport** qui accepte un paramètre et retourne une collection de types d’entités :</span><span class="sxs-lookup"><span data-stu-id="9f350-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="9f350-581">Key, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-581">Key Element (CSDL)</span></span>

<span data-ttu-id="9f350-582">L’élément **Key** est un élément enfant de l’élément EntityType et définit une *clé d’entité* (une propriété ou un ensemble de propriétés d’un type d’entité qui détermine l’identité).</span><span class="sxs-lookup"><span data-stu-id="9f350-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="9f350-583">Les propriétés qui composent une clé d'entité sont choisies au moment du design.</span><span class="sxs-lookup"><span data-stu-id="9f350-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="9f350-584">Les valeurs des propriétés de clé d'entité doivent identifier de façon unique une instance de type d'entité dans un jeu d'entités au moment de l'exécution.</span><span class="sxs-lookup"><span data-stu-id="9f350-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="9f350-585">Les propriétés qui composent une clé d'entité doivent être choisies pour garantir l'unicité des instances dans un jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="9f350-586">L’élément **Key** définit une clé d’entité en référençant une ou plusieurs des propriétés d’un type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="9f350-587">L’élément **Key** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="9f350-588">PropertyRef (un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9f350-589">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-590">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-590">Applicable Attributes</span></span>

<span data-ttu-id="9f350-591">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Key** .</span><span class="sxs-lookup"><span data-stu-id="9f350-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="9f350-592">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-593">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-594">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-594">Example</span></span>

<span data-ttu-id="9f350-595">L’exemple ci-dessous définit un type **d'** entité nommé Book.</span><span class="sxs-lookup"><span data-stu-id="9f350-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="9f350-596">La clé d’entité est définie en référençant la propriété **ISBN** du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="9f350-597">La propriété **ISBN** est un bon choix pour la clé d’entité, car un numéro ISBN (International Standard Book Number) identifie de manière unique un livre.</span><span class="sxs-lookup"><span data-stu-id="9f350-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="9f350-598">L’exemple suivant montre un type d’entité (**auteur**) qui a une clé d’entité composée de deux propriétés, **nom** et **adresse**.</span><span class="sxs-lookup"><span data-stu-id="9f350-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="9f350-599">L’utilisation du **nom** et de l' **adresse** pour la clé d’entité est un choix raisonnable, car il est peu probable que deux auteurs du même nom vivent à la même adresse.</span><span class="sxs-lookup"><span data-stu-id="9f350-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="9f350-600">Toutefois, ce choix pour une clé d'entité ne garantit pas vraiment l'unicité des clés d'entité dans un jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="9f350-601">L’ajout d’une **propriété, telle**que la réutilisation, qui pourrait être utilisée pour identifier un auteur de manière unique, est recommandé dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="9f350-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="9f350-602">Member, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-602">Member Element (CSDL)</span></span>

<span data-ttu-id="9f350-603">L’élément **member** est un élément enfant de l’élément enumType et définit un membre du type énuméré.</span><span class="sxs-lookup"><span data-stu-id="9f350-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-604">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-604">Applicable Attributes</span></span>

<span data-ttu-id="9f350-605">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="9f350-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="9f350-606">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-606">Attribute Name</span></span> | <span data-ttu-id="9f350-607">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-607">Is Required</span></span> | <span data-ttu-id="9f350-608">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-609">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-609">**Name**</span></span>       | <span data-ttu-id="9f350-610">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-610">Yes</span></span>         | <span data-ttu-id="9f350-611">Nom du membre.</span><span class="sxs-lookup"><span data-stu-id="9f350-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="9f350-612">**Valeur**</span><span class="sxs-lookup"><span data-stu-id="9f350-612">**Value**</span></span>      | <span data-ttu-id="9f350-613">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-613">No</span></span>          | <span data-ttu-id="9f350-614">Valeur du membre.</span><span class="sxs-lookup"><span data-stu-id="9f350-614">The value of the member.</span></span> <span data-ttu-id="9f350-615">Par défaut, le premier membre a la valeur 0, et la valeur de chaque énumérateur successif est incrémentée de 1.</span><span class="sxs-lookup"><span data-stu-id="9f350-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="9f350-616">Plusieurs membres ayant les mêmes valeurs peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="9f350-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-617">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="9f350-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="9f350-618">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-619">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-620">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-620">Example</span></span>

<span data-ttu-id="9f350-621">L’exemple suivant montre un élément **enumType** avec trois éléments **membres** :</span><span class="sxs-lookup"><span data-stu-id="9f350-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="9f350-622">NavigationProperty, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="9f350-623">Un élément **NavigationProperty** définit une propriété de navigation, qui fournit une référence à l’autre terminaison d’une association.</span><span class="sxs-lookup"><span data-stu-id="9f350-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="9f350-624">Contrairement aux propriétés définies à l'aide de l'élément Property, les propriétés de navigation ne définissent pas la forme et les caractéristiques des données.</span><span class="sxs-lookup"><span data-stu-id="9f350-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="9f350-625">Elles fournissent un moyen d'explorer une association entre deux types d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="9f350-626">Notez que les propriétés de navigation sont facultatives sur les deux types d'entités au niveau des terminaisons d'une association.</span><span class="sxs-lookup"><span data-stu-id="9f350-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="9f350-627">Si vous définissez une propriété de navigation sur un type d'entité au niveau de la terminaison d'une association, il n'est pas nécessaire de définir une propriété de navigation sur le type d'entité à l'autre terminaison de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="9f350-628">Le type de données retourné par une propriété de navigation est déterminé par la multiplicité de sa terminaison d'association distante.</span><span class="sxs-lookup"><span data-stu-id="9f350-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="9f350-629">Par exemple, supposons qu’une propriété de navigation, **OrdersNavProp**, existe sur un type d’entité **Customer** et navigue vers une association un-à-plusieurs entre **Customer** et **Order**.</span><span class="sxs-lookup"><span data-stu-id="9f350-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="9f350-630">Étant donné que la terminaison d’association distante pour la propriété de navigation possède la multiplicité many ( \* ), son type de données est une collection (of **Order**).</span><span class="sxs-lookup"><span data-stu-id="9f350-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="9f350-631">De même, si une propriété de navigation, **CustomerNavProp**, existe sur le type d’entité **Order** , son type de données est **Customer** , car la multiplicité de la terminaison distante est un (1).</span><span class="sxs-lookup"><span data-stu-id="9f350-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="9f350-632">Un élément **NavigationProperty** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-633">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-634">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-635">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-635">Applicable Attributes</span></span>

<span data-ttu-id="9f350-636">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="9f350-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="9f350-637">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-637">Attribute Name</span></span>   | <span data-ttu-id="9f350-638">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-638">Is Required</span></span> | <span data-ttu-id="9f350-639">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-640">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-640">**Name**</span></span>         | <span data-ttu-id="9f350-641">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-641">Yes</span></span>         | <span data-ttu-id="9f350-642">Nom de la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="9f350-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="9f350-643">**Relationship**</span><span class="sxs-lookup"><span data-stu-id="9f350-643">**Relationship**</span></span> | <span data-ttu-id="9f350-644">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-644">Yes</span></span>         | <span data-ttu-id="9f350-645">Nom d'une association figurant dans l'étendue du modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="9f350-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="9f350-646">**ToRole**</span></span>       | <span data-ttu-id="9f350-647">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-647">Yes</span></span>         | <span data-ttu-id="9f350-648">Terminaison de l'association à laquelle la navigation prend fin.</span><span class="sxs-lookup"><span data-stu-id="9f350-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="9f350-649">La valeur de l’attribut **ToRole** doit être identique à la valeur de l’un des attributs de **rôle** définis sur l’une des terminaisons d’Association (définie dans l’élément AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="9f350-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="9f350-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="9f350-650">**FromRole**</span></span>     | <span data-ttu-id="9f350-651">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-651">Yes</span></span>         | <span data-ttu-id="9f350-652">Terminaison de l'association où la navigation commence.</span><span class="sxs-lookup"><span data-stu-id="9f350-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="9f350-653">La valeur de l’attribut **FromRole** doit être identique à la valeur de l’un des attributs de **rôle** définis sur l’une des terminaisons d’Association (définie dans l’élément AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="9f350-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-654">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="9f350-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="9f350-655">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-656">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-657">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-657">Example</span></span>

<span data-ttu-id="9f350-658">L’exemple suivant définit un type d’entité (**Book**) avec deux propriétés de navigation (**PublishedBy** et **WrittenBy**) :</span><span class="sxs-lookup"><span data-stu-id="9f350-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="9f350-659">OnDelete, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="9f350-660">L’élément **OnDelete** en Conceptual Schema Definition Language (CSDL) définit le comportement qui est connecté avec une association.</span><span class="sxs-lookup"><span data-stu-id="9f350-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="9f350-661">Si l’attribut **action** est défini sur **cascade** à une terminaison d’une association, les types d’entités associés à l’autre terminaison de l’Association sont supprimés lorsque le type d’entité de la première terminaison est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="9f350-662">Si l’association entre deux types d’entités est une relation clé primaire-clé primaire, un objet dépendant chargé est supprimé lorsque l’objet principal de l’autre terminaison de l’Association est supprimé, quelle que soit la spécification de **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="9f350-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="9f350-663">L’élément **OnDelete** affecte uniquement le comportement au moment de l’exécution d’une application ; elle n’affecte pas le comportement dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="9f350-664">Le comportement défini dans la source de données doit être le même que le comportement défini dans l'application.</span><span class="sxs-lookup"><span data-stu-id="9f350-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="9f350-665">Un élément **OnDelete** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-666">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-667">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-668">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-668">Applicable Attributes</span></span>

<span data-ttu-id="9f350-669">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="9f350-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="9f350-670">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-670">Attribute Name</span></span> | <span data-ttu-id="9f350-671">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-671">Is Required</span></span> | <span data-ttu-id="9f350-672">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-673">**Action**</span><span class="sxs-lookup"><span data-stu-id="9f350-673">**Action**</span></span>     | <span data-ttu-id="9f350-674">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-674">Yes</span></span>         | <span data-ttu-id="9f350-675">**Cascade** ou **None**.</span><span class="sxs-lookup"><span data-stu-id="9f350-675">**Cascade** or **None**.</span></span> <span data-ttu-id="9f350-676">Si vous disposez d’une **cascade**, les types d’entités dépendants sont supprimés lorsque le type d’entité principal est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="9f350-677">Si **aucune**valeur n’est, les types d’entités dépendants ne sont pas supprimés lorsque le type d’entité principal est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-678">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="9f350-679">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-680">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-681">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-681">Example</span></span>

<span data-ttu-id="9f350-682">L’exemple suivant montre un élément **Association** qui définit l’Association **customerOrders** .</span><span class="sxs-lookup"><span data-stu-id="9f350-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="9f350-683">L’élément **OnDelete** indique que toutes les **commandes** associées à un **client** particulier et qui ont été chargées dans ObjectContext seront supprimées lors de la suppression du **client** .</span><span class="sxs-lookup"><span data-stu-id="9f350-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="9f350-684">Parameter, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="9f350-685">L’élément **Parameter** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément FunctionImport ou de l’élément Function.</span><span class="sxs-lookup"><span data-stu-id="9f350-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="9f350-686">Application de l'élément FunctionImport</span><span class="sxs-lookup"><span data-stu-id="9f350-686">FunctionImport Element Application</span></span>

<span data-ttu-id="9f350-687">Un élément **Parameter** (en tant qu’enfant de l’élément **FunctionImport** ) est utilisé pour définir les paramètres d’entrée et de sortie des importations de fonctions déclarées dans le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="9f350-688">L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-689">Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-690">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-691">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-691">Applicable Attributes</span></span>

<span data-ttu-id="9f350-692">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="9f350-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="9f350-693">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-693">Attribute Name</span></span> | <span data-ttu-id="9f350-694">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-694">Is Required</span></span> | <span data-ttu-id="9f350-695">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-696">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-696">**Name**</span></span>       | <span data-ttu-id="9f350-697">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-697">Yes</span></span>         | <span data-ttu-id="9f350-698">Le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9f350-699">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-699">**Type**</span></span>       | <span data-ttu-id="9f350-700">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-700">Yes</span></span>         | <span data-ttu-id="9f350-701">Le type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-701">The parameter type.</span></span> <span data-ttu-id="9f350-702">La valeur doit être de type **EDMSimpleType** ou de type complexe, dans la portée du modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="9f350-703">**Mode**</span><span class="sxs-lookup"><span data-stu-id="9f350-703">**Mode**</span></span>       | <span data-ttu-id="9f350-704">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-704">No</span></span>          | <span data-ttu-id="9f350-705">**In**, **out**ou **INOUT** selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie.</span><span class="sxs-lookup"><span data-stu-id="9f350-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="9f350-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-706">**MaxLength**</span></span>  | <span data-ttu-id="9f350-707">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-707">No</span></span>          | <span data-ttu-id="9f350-708">La longueur maximale autorisée du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="9f350-709">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-709">**Precision**</span></span>  | <span data-ttu-id="9f350-710">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-710">No</span></span>          | <span data-ttu-id="9f350-711">La précision du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-712">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-712">**Scale**</span></span>      | <span data-ttu-id="9f350-713">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-713">No</span></span>          | <span data-ttu-id="9f350-714">L’échelle du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="9f350-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-715">**SRID**</span></span>       | <span data-ttu-id="9f350-716">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-716">No</span></span>          | <span data-ttu-id="9f350-717">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-718">Valide uniquement pour les paramètres des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="9f350-719">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-720">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="9f350-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="9f350-721">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-722">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-723">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-723">Example</span></span>

<span data-ttu-id="9f350-724">L’exemple suivant montre un élément **FunctionImport** avec un élément enfant **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="9f350-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="9f350-725">La fonction accepte un paramètre d'entrée et retourne une collection de types d'entités.</span><span class="sxs-lookup"><span data-stu-id="9f350-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="9f350-726">Application de l'élément Function</span><span class="sxs-lookup"><span data-stu-id="9f350-726">Function Element Application</span></span>

<span data-ttu-id="9f350-727">Un élément **Parameter** (en tant qu’enfant de l’élément **Function** ) définit des paramètres pour les fonctions qui sont définies ou déclarées dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="9f350-728">L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-729">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="9f350-730">CollectionType (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="9f350-731">ReferenceType (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="9f350-732">RowType (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-733">Seul l’un des éléments **CollectionType**, **ReferenceType**ou **RowType** peut être un élément enfant d’un élément de **propriété** .</span><span class="sxs-lookup"><span data-stu-id="9f350-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="9f350-734">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-735">Les éléments d'annotation doivent figurer après tous les autres éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="9f350-736">Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9f350-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-737">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-737">Applicable Attributes</span></span>

<span data-ttu-id="9f350-738">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="9f350-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="9f350-739">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-739">Attribute Name</span></span>   | <span data-ttu-id="9f350-740">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-740">Is Required</span></span> | <span data-ttu-id="9f350-741">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-742">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-742">**Name**</span></span>         | <span data-ttu-id="9f350-743">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-743">Yes</span></span>         | <span data-ttu-id="9f350-744">Le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9f350-745">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-745">**Type**</span></span>         | <span data-ttu-id="9f350-746">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-746">No</span></span>          | <span data-ttu-id="9f350-747">Le type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f350-747">The parameter type.</span></span> <span data-ttu-id="9f350-748">Un paramètre peut correspondre à l'un quelconque des types suivants (ou à des collections de ces types) :</span><span class="sxs-lookup"><span data-stu-id="9f350-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="9f350-749">**Type EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="9f350-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="9f350-750">type d'entité</span><span class="sxs-lookup"><span data-stu-id="9f350-750">entity type</span></span> <br/> <span data-ttu-id="9f350-751">type complexe</span><span class="sxs-lookup"><span data-stu-id="9f350-751">complex type</span></span> <br/> <span data-ttu-id="9f350-752">type de ligne</span><span class="sxs-lookup"><span data-stu-id="9f350-752">row type</span></span> <br/> <span data-ttu-id="9f350-753">type référence</span><span class="sxs-lookup"><span data-stu-id="9f350-753">reference type</span></span>                             |
| <span data-ttu-id="9f350-754">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-754">**Nullable**</span></span>     | <span data-ttu-id="9f350-755">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-755">No</span></span>          | <span data-ttu-id="9f350-756">**True** (valeur par défaut) ou **false** selon que la propriété peut avoir une valeur **null** ou non.</span><span class="sxs-lookup"><span data-stu-id="9f350-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="9f350-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9f350-757">**DefaultValue**</span></span> | <span data-ttu-id="9f350-758">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-758">No</span></span>          | <span data-ttu-id="9f350-759">Valeur par défaut de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9f350-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-760">**MaxLength**</span></span>    | <span data-ttu-id="9f350-761">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-761">No</span></span>          | <span data-ttu-id="9f350-762">Longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9f350-763">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-763">**FixedLength**</span></span>  | <span data-ttu-id="9f350-764">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-764">No</span></span>          | <span data-ttu-id="9f350-765">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="9f350-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9f350-766">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-766">**Precision**</span></span>    | <span data-ttu-id="9f350-767">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-767">No</span></span>          | <span data-ttu-id="9f350-768">Précision de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9f350-769">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-769">**Scale**</span></span>        | <span data-ttu-id="9f350-770">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-770">No</span></span>          | <span data-ttu-id="9f350-771">Échelle de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9f350-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-772">**SRID**</span></span>         | <span data-ttu-id="9f350-773">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-773">No</span></span>          | <span data-ttu-id="9f350-774">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-775">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9f350-776">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9f350-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-777">**Unicode**</span></span>      | <span data-ttu-id="9f350-778">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-778">No</span></span>          | <span data-ttu-id="9f350-779">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9f350-780">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-780">**Collation**</span></span>    | <span data-ttu-id="9f350-781">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-781">No</span></span>          | <span data-ttu-id="9f350-782">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9f350-783">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="9f350-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="9f350-784">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-785">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-786">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-786">Example</span></span>

<span data-ttu-id="9f350-787">L’exemple suivant montre un élément **Function** qui utilise un élément enfant **Parameter** pour définir un paramètre de fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="9f350-788">Principal, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="9f350-789">L’élément **principal** en Conceptual Schema Definition Language (CSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison principale d’une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="9f350-790">Un élément **ReferentialConstraint** définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="9f350-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9f350-791">De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9f350-792">Le type d’entité référencé est appelé *terminaison principale* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9f350-793">Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="9f350-794">Les éléments **PropertyRef** sont utilisés pour spécifier les clés qui sont référencées par la terminaison dépendante.</span><span class="sxs-lookup"><span data-stu-id="9f350-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="9f350-795">L’élément **principal** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-796">PropertyRef (un ou plusieurs éléments) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9f350-797">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-798">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-798">Applicable Attributes</span></span>

<span data-ttu-id="9f350-799">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="9f350-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="9f350-800">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-800">Attribute Name</span></span> | <span data-ttu-id="9f350-801">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-801">Is Required</span></span> | <span data-ttu-id="9f350-802">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="9f350-803">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="9f350-803">**Role**</span></span>       | <span data-ttu-id="9f350-804">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-804">Yes</span></span>         | <span data-ttu-id="9f350-805">Nom du type d'entité au niveau de la terminaison principale de l'association.</span><span class="sxs-lookup"><span data-stu-id="9f350-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-806">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="9f350-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="9f350-807">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-808">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-809">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-809">Example</span></span>

<span data-ttu-id="9f350-810">L’exemple suivant montre un élément **ReferentialConstraint** qui fait partie de la définition de l’Association **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="9f350-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="9f350-811">La propriété **ID** du type d’entité du serveur de **publication** compose la terminaison principale de la contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="9f350-812">Property, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-812">Property Element (CSDL)</span></span>

<span data-ttu-id="9f350-813">L’élément de **propriété** en Conceptual Schema Definition Language (CSDL) peut être un enfant de l’élément EntityType, de l’élément complexType ou de l’élément RowType.</span><span class="sxs-lookup"><span data-stu-id="9f350-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="9f350-814">Applications des éléments EntityType et ComplexType</span><span class="sxs-lookup"><span data-stu-id="9f350-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="9f350-815">Les éléments de **propriété** (en tant qu’enfants des éléments **EntityType** ou **complexType** ) définissent la forme et les caractéristiques des données qu’une instance de type d’entité ou une instance de type complexe contiendra.</span><span class="sxs-lookup"><span data-stu-id="9f350-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="9f350-816">Les propriétés dans un modèle conceptuel sont analogues aux propriétés qui sont définies sur une classe.</span><span class="sxs-lookup"><span data-stu-id="9f350-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="9f350-817">De même que les propriétés sur une classe définissent la forme de la classe et acheminent des informations sur les objets, les propriétés dans un modèle conceptuel définissent la forme d'un type d'entité et acheminent des informations sur les instances de type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="9f350-818">L’élément **Property** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-819">élément Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-820">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="9f350-821">Les facettes suivantes peuvent être appliquées à un élément **Property** : **Nullable**, **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**, **collation**, **ConcurrencyMode**.</span><span class="sxs-lookup"><span data-stu-id="9f350-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="9f350-822">Les facettes sont des attributs XML qui fournissent des informations sur la manière dont les valeurs de propriété sont stockées dans la banque de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-823">Les facettes ne peuvent être appliquées qu’à des propriétés de type **type EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="9f350-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-824">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-824">Applicable Attributes</span></span>

<span data-ttu-id="9f350-825">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="9f350-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="9f350-826">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-826">Attribute Name</span></span>                                                         | <span data-ttu-id="9f350-827">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-827">Is Required</span></span> | <span data-ttu-id="9f350-828">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-829">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-829">**Name**</span></span>                                                               | <span data-ttu-id="9f350-830">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-830">Yes</span></span>         | <span data-ttu-id="9f350-831">Nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="9f350-832">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-832">**Type**</span></span>                                                               | <span data-ttu-id="9f350-833">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-833">Yes</span></span>         | <span data-ttu-id="9f350-834">Type de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-834">The type of the property value.</span></span> <span data-ttu-id="9f350-835">Le type de la valeur de propriété doit être un type **EDMSimpleType** ou un type complexe (indiqué par un nom qualifié complet) qui se trouve dans la portée du modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="9f350-836">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-836">**Nullable**</span></span>                                                           | <span data-ttu-id="9f350-837">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-837">No</span></span>          | <span data-ttu-id="9f350-838">**True** (valeur par défaut) ou <strong>False</strong> selon que la propriété peut avoir ou non une valeur null.</span><span class="sxs-lookup"><span data-stu-id="9f350-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="9f350-839">> dans le langage CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="9f350-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9f350-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="9f350-841">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-841">No</span></span>          | <span data-ttu-id="9f350-842">Valeur par défaut de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9f350-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="9f350-844">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-844">No</span></span>          | <span data-ttu-id="9f350-845">Longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9f350-846">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="9f350-847">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-847">No</span></span>          | <span data-ttu-id="9f350-848">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="9f350-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9f350-849">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-849">**Precision**</span></span>                                                          | <span data-ttu-id="9f350-850">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-850">No</span></span>          | <span data-ttu-id="9f350-851">Précision de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9f350-852">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-852">**Scale**</span></span>                                                              | <span data-ttu-id="9f350-853">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-853">No</span></span>          | <span data-ttu-id="9f350-854">Échelle de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9f350-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-855">**SRID**</span></span>                                                               | <span data-ttu-id="9f350-856">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-856">No</span></span>          | <span data-ttu-id="9f350-857">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-858">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9f350-859">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9f350-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-860">**Unicode**</span></span>                                                            | <span data-ttu-id="9f350-861">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-861">No</span></span>          | <span data-ttu-id="9f350-862">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9f350-863">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-863">**Collation**</span></span>                                                          | <span data-ttu-id="9f350-864">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-864">No</span></span>          | <span data-ttu-id="9f350-865">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="9f350-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="9f350-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="9f350-867">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-867">No</span></span>          | <span data-ttu-id="9f350-868">**None** (valeur par défaut) ou **Fixed**.</span><span class="sxs-lookup"><span data-stu-id="9f350-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="9f350-869">Si la valeur est définie sur **Fixed**, la valeur de propriété sera utilisée dans les contrôles d’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="9f350-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="9f350-870">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="9f350-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="9f350-871">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-872">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-873">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-873">Example</span></span>

<span data-ttu-id="9f350-874">L’exemple suivant montre un élément **EntityType** avec trois éléments **Property** :</span><span class="sxs-lookup"><span data-stu-id="9f350-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="9f350-875">L’exemple suivant montre un élément **complexType** avec cinq éléments de **propriété** :</span><span class="sxs-lookup"><span data-stu-id="9f350-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="9f350-876">Application de l'élément RowType</span><span class="sxs-lookup"><span data-stu-id="9f350-876">RowType Element Application</span></span>

<span data-ttu-id="9f350-877">Les éléments de **propriété** (comme les enfants d’un élément **RowType** ) définissent la forme et les caractéristiques des données qui peuvent être passées ou retournées à partir d’une fonction définie par modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="9f350-878">L’élément **Property** peut avoir exactement l’un des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="9f350-879">CollectionType ;</span><span class="sxs-lookup"><span data-stu-id="9f350-879">CollectionType</span></span>
-   <span data-ttu-id="9f350-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9f350-880">ReferenceType</span></span>
-   <span data-ttu-id="9f350-881">RowType</span><span class="sxs-lookup"><span data-stu-id="9f350-881">RowType</span></span>

<span data-ttu-id="9f350-882">L’élément **Property** peut avoir n’importe quel nombre d’éléments d’annotation enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-883">Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9f350-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9f350-884">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-884">Applicable Attributes</span></span>

<span data-ttu-id="9f350-885">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="9f350-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="9f350-886">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-886">Attribute Name</span></span>                                                     | <span data-ttu-id="9f350-887">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-887">Is Required</span></span> | <span data-ttu-id="9f350-888">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-889">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-889">**Name**</span></span>                                                           | <span data-ttu-id="9f350-890">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-890">Yes</span></span>         | <span data-ttu-id="9f350-891">Nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="9f350-892">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-892">**Type**</span></span>                                                           | <span data-ttu-id="9f350-893">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-893">Yes</span></span>         | <span data-ttu-id="9f350-894">Type de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-895">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-895">**Nullable**</span></span>                                                       | <span data-ttu-id="9f350-896">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-896">No</span></span>          | <span data-ttu-id="9f350-897">**True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null.</span><span class="sxs-lookup"><span data-stu-id="9f350-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="9f350-898">> dans CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="9f350-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9f350-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="9f350-900">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-900">No</span></span>          | <span data-ttu-id="9f350-901">Valeur par défaut de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9f350-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="9f350-903">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-903">No</span></span>          | <span data-ttu-id="9f350-904">Longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9f350-905">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="9f350-906">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-906">No</span></span>          | <span data-ttu-id="9f350-907">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="9f350-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9f350-908">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-908">**Precision**</span></span>                                                      | <span data-ttu-id="9f350-909">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-909">No</span></span>          | <span data-ttu-id="9f350-910">Précision de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9f350-911">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-911">**Scale**</span></span>                                                          | <span data-ttu-id="9f350-912">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-912">No</span></span>          | <span data-ttu-id="9f350-913">Échelle de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9f350-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-914">**SRID**</span></span>                                                           | <span data-ttu-id="9f350-915">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-915">No</span></span>          | <span data-ttu-id="9f350-916">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-917">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9f350-918">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9f350-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-919">**Unicode**</span></span>                                                        | <span data-ttu-id="9f350-920">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-920">No</span></span>          | <span data-ttu-id="9f350-921">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9f350-922">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-922">**Collation**</span></span>                                                      | <span data-ttu-id="9f350-923">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-923">No</span></span>          | <span data-ttu-id="9f350-924">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9f350-925">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="9f350-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="9f350-926">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-927">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9f350-928">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-928">Example</span></span>

<span data-ttu-id="9f350-929">L’exemple suivant montre les éléments de **propriété** utilisés pour définir la forme du type de retour d’une fonction définie par modèle.</span><span class="sxs-lookup"><span data-stu-id="9f350-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="9f350-930">PropertyRef, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="9f350-931">L’élément **PropertyRef** en Conceptual Schema Definition Language (CSDL) fait référence à une propriété d’un type d’entité pour indiquer que la propriété effectuera l’un des rôles suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="9f350-932">Partie de la clé de l'entité (une propriété ou un jeu de propriétés d'un type d'entité qui détermine l'identité).</span><span class="sxs-lookup"><span data-stu-id="9f350-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="9f350-933">Un ou plusieurs éléments **PropertyRef** peuvent être utilisés pour définir une clé d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="9f350-934">Terminaison dépendante ou principale d'une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="9f350-935">L’élément **PropertyRef** peut uniquement comporter des éléments d’annotation (zéro ou plus) en tant qu’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-936">Les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9f350-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-937">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-937">Applicable Attributes</span></span>

<span data-ttu-id="9f350-938">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="9f350-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="9f350-939">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-939">Attribute Name</span></span> | <span data-ttu-id="9f350-940">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-940">Is Required</span></span> | <span data-ttu-id="9f350-941">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="9f350-942">**Nom**</span><span class="sxs-lookup"><span data-stu-id="9f350-942">**Name**</span></span>       | <span data-ttu-id="9f350-943">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-943">Yes</span></span>         | <span data-ttu-id="9f350-944">Nom de la propriété référencée.</span><span class="sxs-lookup"><span data-stu-id="9f350-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-945">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="9f350-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="9f350-946">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-947">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-948">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-948">Example</span></span>

<span data-ttu-id="9f350-949">L’exemple ci-dessous définit un type**d'** entité (Book).</span><span class="sxs-lookup"><span data-stu-id="9f350-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="9f350-950">La clé d’entité est définie en référençant la propriété **ISBN** du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="9f350-951">Dans l’exemple suivant, deux éléments **PropertyRef** sont utilisés pour indiquer que deux propriétés (**ID** et **PublisherId**) sont les terminaisons principale et dépendante d’une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="9f350-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="9f350-952">Élément ReferenceType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="9f350-953">L’élément **ReferenceType** en Conceptual Schema Definition Language (CSDL) spécifie une référence à un type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="9f350-954">L’élément **ReferenceType** peut être un enfant des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="9f350-955">ReturnType (fonction)</span><span class="sxs-lookup"><span data-stu-id="9f350-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="9f350-956">Paramètre</span><span class="sxs-lookup"><span data-stu-id="9f350-956">Parameter</span></span>
-   <span data-ttu-id="9f350-957">CollectionType ;</span><span class="sxs-lookup"><span data-stu-id="9f350-957">CollectionType</span></span>

<span data-ttu-id="9f350-958">L’élément **ReferenceType** est utilisé lors de la définition d’un paramètre ou d’un type de retour pour une fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="9f350-959">Un élément **ReferenceType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-960">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-961">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-962">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-962">Applicable Attributes</span></span>

<span data-ttu-id="9f350-963">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="9f350-964">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-964">Attribute Name</span></span> | <span data-ttu-id="9f350-965">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-965">Is Required</span></span> | <span data-ttu-id="9f350-966">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="9f350-967">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-967">**Type**</span></span>       | <span data-ttu-id="9f350-968">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-968">Yes</span></span>         | <span data-ttu-id="9f350-969">Nom du type d'entité référencé.</span><span class="sxs-lookup"><span data-stu-id="9f350-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-970">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="9f350-971">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-972">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-973">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-973">Example</span></span>

<span data-ttu-id="9f350-974">L’exemple suivant montre l’élément **ReferenceType** utilisé comme enfant d’un élément **Parameter** dans une fonction définie par modèle qui accepte une référence à un type d’entité **Person** :</span><span class="sxs-lookup"><span data-stu-id="9f350-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="9f350-975">L’exemple suivant montre l’élément **ReferenceType** utilisé comme enfant d’un élément **ReturnType** (Function) dans une fonction définie par modèle qui retourne une référence à un type d’entité **Person** :</span><span class="sxs-lookup"><span data-stu-id="9f350-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="9f350-976">ReferentialConstraint, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="9f350-977">Un élément **ReferentialConstraint** en Conceptual Schema Definition Language (CSDL) définit des fonctionnalités qui sont semblables à une contrainte d’intégrité référentielle dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="9f350-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9f350-978">De la même manière qu'une ou plusieurs colonnes d'une table de base de données peuvent référencer la clé primaire d'une autre table, une ou plusieurs propriétés d'un type d'entité peuvent référencer la clé d'entité d'un autre type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9f350-979">Le type d’entité référencé est appelé *terminaison principale* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9f350-980">Le type d’entité qui référence la terminaison principale est appelé *terminaison dépendante* de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="9f350-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="9f350-981">Si une clé étrangère exposée sur un type d’entité fait référence à une propriété sur un autre type d’entité, l’élément **ReferentialConstraint** définit une association entre les deux types d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="9f350-982">Étant donné que l’élément **ReferentialConstraint** fournit des informations sur la façon dont deux types d’entité sont associés, aucun élément AssociationSetMapping correspondant n’est nécessaire dans le Mapping Specification Language (MSL).</span><span class="sxs-lookup"><span data-stu-id="9f350-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="9f350-983">Une association entre deux types d’entités qui n’ont pas de clés étrangères exposées doit avoir un élément **AssociationSetMapping** correspondant pour mapper les informations d’association à la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="9f350-984">Si une clé étrangère n’est pas exposée sur un type d’entité, l’élément **ReferentialConstraint** ne peut définir qu’une contrainte de clé primaire-clé primaire entre le type d’entité et un autre type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="9f350-985">Un élément **ReferentialConstraint** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-986">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-987">Principal (exactement un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="9f350-988">Dépendent (exactement un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="9f350-989">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-990">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-990">Applicable Attributes</span></span>

<span data-ttu-id="9f350-991">L’élément **ReferentialConstraint** peut avoir n’importe quel nombre d’attributs d’annotation (attributs XML personnalisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="9f350-992">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-993">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-994">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-994">Example</span></span>

<span data-ttu-id="9f350-995">L’exemple suivant montre un élément **ReferentialConstraint** utilisé dans le cadre de la définition de l’Association **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="9f350-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="9f350-996">ReturnType (Function), élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="9f350-997">L’élément **ReturnType** (Function) en Conceptual Schema Definition Language (CSDL) spécifie le type de retour pour une fonction définie dans un élément Function.</span><span class="sxs-lookup"><span data-stu-id="9f350-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="9f350-998">Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="9f350-999">Les types de retour peuvent être n’importe quel **type EDMSimpleType**, type d’entité, type complexe, type de ligne, type ref ou une collection de l’un de ces types.</span><span class="sxs-lookup"><span data-stu-id="9f350-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="9f350-1000">Le type de retour d’une fonction peut être spécifié avec l’attribut de **type** de l’élément **ReturnType** (Function), ou avec l’un des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="9f350-1001">CollectionType ;</span><span class="sxs-lookup"><span data-stu-id="9f350-1001">CollectionType</span></span>
-   <span data-ttu-id="9f350-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9f350-1002">ReferenceType</span></span>
-   <span data-ttu-id="9f350-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="9f350-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-1004">Un modèle ne sera pas validé si vous spécifiez un type de retour de fonction avec à la fois l’attribut de **type** de l’élément **ReturnType** (Function) et l’un des éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="9f350-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1005">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1005">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1006">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **ReturnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="9f350-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="9f350-1007">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-1007">Attribute Name</span></span> | <span data-ttu-id="9f350-1008">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-1008">Is Required</span></span> | <span data-ttu-id="9f350-1009">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="9f350-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9f350-1010">**ReturnType**</span></span> | <span data-ttu-id="9f350-1011">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1011">No</span></span>          | <span data-ttu-id="9f350-1012">Type retourné par la fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-1013">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="9f350-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="9f350-1014">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1015">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-1016">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1016">Example</span></span>

<span data-ttu-id="9f350-1017">L’exemple suivant utilise un élément **Function** pour définir une fonction qui retourne le nombre d’années pendant lequel un livre a été imprimé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="9f350-1018">Notez que le type de retour est spécifié par l’attribut **type** d’un élément **ReturnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="9f350-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="9f350-1019">ReturnType (FunctionImport), élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="9f350-1020">L’élément **ReturnType** (FunctionImport) de Conceptual Schema Definition Language (CSDL) spécifie le type de retour pour une fonction définie dans un élément FunctionImport.</span><span class="sxs-lookup"><span data-stu-id="9f350-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="9f350-1021">Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="9f350-1022">Les types de retour peuvent être n’importe quelle collection de type d’entité, de type complexe ou de **type EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="9f350-1023">Le type de retour d’une fonction est spécifié avec l’attribut de **type** de l’élément **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="9f350-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1024">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1024">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1025">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="9f350-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="9f350-1026">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-1026">Attribute Name</span></span> | <span data-ttu-id="9f350-1027">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-1027">Is Required</span></span> | <span data-ttu-id="9f350-1028">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-1029">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-1029">**Type**</span></span>       | <span data-ttu-id="9f350-1030">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1030">No</span></span>          | <span data-ttu-id="9f350-1031">Type retourné par la fonction.</span><span class="sxs-lookup"><span data-stu-id="9f350-1031">The type that the function returns.</span></span> <span data-ttu-id="9f350-1032">La valeur doit être une collection de ComplexType, EntityType ou type EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="9f350-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="9f350-1033">**EntitySet ;**</span><span class="sxs-lookup"><span data-stu-id="9f350-1033">**EntitySet**</span></span>  | <span data-ttu-id="9f350-1034">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1034">No</span></span>          | <span data-ttu-id="9f350-1035">Si la fonction retourne une collection de types d’entités, la valeur de l' **EntitySet** doit être le jeu d’entités auquel la collection appartient.</span><span class="sxs-lookup"><span data-stu-id="9f350-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="9f350-1036">Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-1037">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="9f350-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="9f350-1038">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1039">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-1040">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1040">Example</span></span>

<span data-ttu-id="9f350-1041">L’exemple suivant utilise un **FunctionImport** qui retourne des livres et des serveurs de publication.</span><span class="sxs-lookup"><span data-stu-id="9f350-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="9f350-1042">Notez que la fonction retourne deux jeux de résultats et, par conséquent, deux éléments **ReturnType** (FunctionImport) sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9f350-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="9f350-1043">Élément RowType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="9f350-1044">Un élément **RowType** en Conceptual Schema Definition Language (CSDL) définit une structure sans nom en tant que paramètre ou type de retour pour une fonction définie dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="9f350-1045">Un élément **RowType** peut être l’enfant des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="9f350-1046">CollectionType ;</span><span class="sxs-lookup"><span data-stu-id="9f350-1046">CollectionType</span></span>
-   <span data-ttu-id="9f350-1047">Paramètre</span><span class="sxs-lookup"><span data-stu-id="9f350-1047">Parameter</span></span>
-   <span data-ttu-id="9f350-1048">ReturnType (fonction)</span><span class="sxs-lookup"><span data-stu-id="9f350-1048">ReturnType (Function)</span></span>

<span data-ttu-id="9f350-1049">Un élément **RowType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-1050">Property (un ou plusieurs) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-1050">Property (one or more)</span></span>
-   <span data-ttu-id="9f350-1051">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="9f350-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1052">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1052">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1053">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **RowType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="9f350-1054">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1055">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-1056">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1056">Example</span></span>

<span data-ttu-id="9f350-1057">L’exemple suivant montre une fonction définie par modèle qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="9f350-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="9f350-1058">Schema, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="9f350-1059">L’élément **Schema** est l’élément racine d’une définition de modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="9f350-1060">Il contient des définitions pour les objets, fonctions et conteneurs qui composent un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="9f350-1061">L’élément **Schema** peut contenir zéro, un ou plusieurs des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="9f350-1062">Utilisation</span><span class="sxs-lookup"><span data-stu-id="9f350-1062">Using</span></span>
-   <span data-ttu-id="9f350-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="9f350-1063">EntityContainer</span></span>
-   <span data-ttu-id="9f350-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="9f350-1064">EntityType</span></span>
-   <span data-ttu-id="9f350-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="9f350-1065">EnumType</span></span>
-   <span data-ttu-id="9f350-1066">Association</span><span class="sxs-lookup"><span data-stu-id="9f350-1066">Association</span></span>
-   <span data-ttu-id="9f350-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="9f350-1067">ComplexType</span></span>
-   <span data-ttu-id="9f350-1068">Fonction</span><span class="sxs-lookup"><span data-stu-id="9f350-1068">Function</span></span>

<span data-ttu-id="9f350-1069">Un élément de **schéma** peut contenir zéro ou un élément d’annotation.</span><span class="sxs-lookup"><span data-stu-id="9f350-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-1070">L’élément de **fonction** et les éléments d’annotation sont autorisés uniquement dans CSDL v2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9f350-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="9f350-1071">L’élément **Schema** utilise l’attribut **namespace** pour définir l’espace de noms pour le type d’entité, le type complexe et les objets Association dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="9f350-1072">Dans un espace de noms, deux objets ne peuvent pas avoir le même nom.</span><span class="sxs-lookup"><span data-stu-id="9f350-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="9f350-1073">Les espaces de noms peuvent s’étendre sur plusieurs éléments de **schéma** et plusieurs fichiers. csdl.</span><span class="sxs-lookup"><span data-stu-id="9f350-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="9f350-1074">Un espace de noms de modèle conceptuel est différent de l’espace de noms XML de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="9f350-1075">Un espace de noms de modèle conceptuel (tel que défini par l’attribut **namespace** ) est un conteneur logique pour les types d’entité, les types complexes et les types d’association.</span><span class="sxs-lookup"><span data-stu-id="9f350-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="9f350-1076">L’espace de noms XML (indiqué par l’attribut **xmlns** ) d’un élément de **schéma** est l’espace de noms par défaut pour les éléments enfants et les attributs de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="9f350-1077">Les espaces de noms XML de la forme https://schemas.microsoft.com/ado/YYYY/MM/edm (où aaaa et mm représentent respectivement une année et un mois) sont réservés au langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="9f350-1078">Des éléments et attributs personnalisés ne peuvent pas être dans des espaces de noms de cette forme.</span><span class="sxs-lookup"><span data-stu-id="9f350-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1079">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1079">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1080">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Schema** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="9f350-1081">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-1081">Attribute Name</span></span> | <span data-ttu-id="9f350-1082">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-1082">Is Required</span></span> | <span data-ttu-id="9f350-1083">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-1084">**Espace de noms**</span><span class="sxs-lookup"><span data-stu-id="9f350-1084">**Namespace**</span></span>  | <span data-ttu-id="9f350-1085">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1085">Yes</span></span>         | <span data-ttu-id="9f350-1086">Espace de noms du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="9f350-1087">La valeur de l’attribut d' **espace de noms** est utilisée pour former le nom qualifié complet d’un type.</span><span class="sxs-lookup"><span data-stu-id="9f350-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="9f350-1088">Par exemple, si un **EntityType** nommé *Customer* est dans l’espace de noms simple. example. Model, le nom qualifié complet de l' **EntityType** est SimpleExampleModel. Customer.</span><span class="sxs-lookup"><span data-stu-id="9f350-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="9f350-1089">Les chaînes suivantes ne peuvent pas être utilisées comme valeur pour l’attribut d' **espace de noms** : **System**, **transient**ou **EDM**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="9f350-1090">La valeur de l’attribut d' **espace de noms** ne peut pas être la même que la valeur de l’attribut d' **espace de noms** dans l’élément de schéma SSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="9f350-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="9f350-1091">**Alias**</span></span>      | <span data-ttu-id="9f350-1092">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1092">No</span></span>          | <span data-ttu-id="9f350-1093">Identificateur utilisé à la place du nom de l'espace de noms.</span><span class="sxs-lookup"><span data-stu-id="9f350-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="9f350-1094">Par exemple, si un **EntityType** nommé *Customer* est dans l’espace de noms simple. example. Model et que la valeur de l’attribut **alias** est *Model*, vous pouvez utiliser Model. Customer comme nom qualifié complet de l' **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="9f350-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="9f350-1095">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="9f350-1096">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1097">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-1098">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1098">Example</span></span>

<span data-ttu-id="9f350-1099">L’exemple suivant illustre un élément de **schéma** qui contient un élément **EntityContainer** , deux éléments **EntityType** et un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="9f350-1100">Élément TypeRef (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="9f350-1101">L’élément **TypeRef** en Conceptual Schema Definition Language (CSDL) fournit une référence à un type nommé existant.</span><span class="sxs-lookup"><span data-stu-id="9f350-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="9f350-1102">L’élément **TypeRef** peut être un enfant de l’élément CollectionType, qui est utilisé pour spécifier qu’une fonction a une collection en tant que paramètre ou type de retour.</span><span class="sxs-lookup"><span data-stu-id="9f350-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="9f350-1103">Un élément **TypeRef** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="9f350-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9f350-1104">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9f350-1105">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="9f350-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1106">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1106">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1107">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **TypeRef** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="9f350-1108">Notez que les attributs **DefaultValue**, **MaxLength**, **multiple**, **PRECISION**, **Scale**, **Unicode**et **collation** sont uniquement applicables à **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="9f350-1109">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="9f350-1110">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-1110">Is Required</span></span> | <span data-ttu-id="9f350-1111">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-1112">**Type**</span><span class="sxs-lookup"><span data-stu-id="9f350-1112">**Type**</span></span>                                                           | <span data-ttu-id="9f350-1113">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1113">No</span></span>          | <span data-ttu-id="9f350-1114">Nom du type référencé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="9f350-1115">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="9f350-1116">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1116">No</span></span>          | <span data-ttu-id="9f350-1117">**True** (valeur par défaut) ou **False** selon que la propriété peut avoir ou non une valeur null.</span><span class="sxs-lookup"><span data-stu-id="9f350-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="9f350-1118">> dans CSDL v1, une propriété de type complexe doit avoir `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="9f350-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9f350-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9f350-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="9f350-1120">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1120">No</span></span>          | <span data-ttu-id="9f350-1121">Valeur par défaut de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9f350-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="9f350-1123">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1123">No</span></span>          | <span data-ttu-id="9f350-1124">Longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9f350-1125">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="9f350-1126">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1126">No</span></span>          | <span data-ttu-id="9f350-1127">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="9f350-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9f350-1128">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-1128">**Precision**</span></span>                                                      | <span data-ttu-id="9f350-1129">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1129">No</span></span>          | <span data-ttu-id="9f350-1130">Précision de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9f350-1131">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-1131">**Scale**</span></span>                                                          | <span data-ttu-id="9f350-1132">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1132">No</span></span>          | <span data-ttu-id="9f350-1133">Échelle de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9f350-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-1134">**SRID**</span></span>                                                           | <span data-ttu-id="9f350-1135">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1135">No</span></span>          | <span data-ttu-id="9f350-1136">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9f350-1137">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="9f350-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9f350-1138">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9f350-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="9f350-1140">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1140">No</span></span>          | <span data-ttu-id="9f350-1141">**True** ou **false** selon que la valeur de la propriété sera stockée ou non comme une chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9f350-1142">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-1142">**Collation**</span></span>                                                      | <span data-ttu-id="9f350-1143">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1143">No</span></span>          | <span data-ttu-id="9f350-1144">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9f350-1145">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9f350-1146">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1147">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-1148">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1148">Example</span></span>

<span data-ttu-id="9f350-1149">L’exemple suivant montre une fonction définie par modèle qui utilise l’élément **TypeRef** (en tant qu’enfant d’un élément **CollectionType** ) pour spécifier que la fonction accepte une collection de types d’entités **Department** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="9f350-1150">Using, élément (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="9f350-1151">L’élément **using** de Conceptual Schema Definition Language (CSDL) importe le contenu d’un modèle conceptuel qui existe dans un espace de noms différent.</span><span class="sxs-lookup"><span data-stu-id="9f350-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="9f350-1152">En définissant la valeur de l’attribut d' **espace de noms** , vous pouvez faire référence à des types d’entités, des types complexes et des types d’associations définis dans un autre modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="9f350-1153">Plus d’un élément **using** peut être un enfant d’un élément **Schema** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-1154">L’élément **using** dans CSDL ne fonctionne pas exactement comme une instruction **using** dans un langage de programmation.</span><span class="sxs-lookup"><span data-stu-id="9f350-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="9f350-1155">En important un espace de noms avec une instruction **using** dans un langage de programmation, vous n’affectez pas les objets de l’espace de noms d’origine.</span><span class="sxs-lookup"><span data-stu-id="9f350-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="9f350-1156">Dans le langage CSDL, un espace de noms importé peut contenir un type d'entité dérivé d'un type d'entité figurant dans l'espace de noms d'origine.</span><span class="sxs-lookup"><span data-stu-id="9f350-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="9f350-1157">Cela peut affecter les jeux d'entités déclarés dans l'espace de noms d'origine.</span><span class="sxs-lookup"><span data-stu-id="9f350-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="9f350-1158">L’élément **using** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="9f350-1159">Documentation (zéro ou un élément autorisé) ;</span><span class="sxs-lookup"><span data-stu-id="9f350-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9f350-1160">éléments d'annotation (zéro, un ou plusieurs éléments autorisés).</span><span class="sxs-lookup"><span data-stu-id="9f350-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9f350-1161">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1161">Applicable Attributes</span></span>

<span data-ttu-id="9f350-1162">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **using** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="9f350-1163">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="9f350-1163">Attribute Name</span></span> | <span data-ttu-id="9f350-1164">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="9f350-1164">Is Required</span></span> | <span data-ttu-id="9f350-1165">Valeur</span><span class="sxs-lookup"><span data-stu-id="9f350-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f350-1166">**Espace de noms**</span><span class="sxs-lookup"><span data-stu-id="9f350-1166">**Namespace**</span></span>  | <span data-ttu-id="9f350-1167">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1167">Yes</span></span>         | <span data-ttu-id="9f350-1168">Nom de l'espace de noms importé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="9f350-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="9f350-1169">**Alias**</span></span>      | <span data-ttu-id="9f350-1170">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1170">Yes</span></span>         | <span data-ttu-id="9f350-1171">Identificateur utilisé à la place du nom de l'espace de noms.</span><span class="sxs-lookup"><span data-stu-id="9f350-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="9f350-1172">Bien que cet attribut soit obligatoire, il n'est pas nécessaire qu'il soit utilisé à la place du nom de l'espace de noms pour qualifier les noms d'objets.</span><span class="sxs-lookup"><span data-stu-id="9f350-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9f350-1173">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **using** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="9f350-1174">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9f350-1175">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9f350-1176">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1176">Example</span></span>

<span data-ttu-id="9f350-1177">L’exemple suivant illustre l' **utilisation** de l’élément Using pour importer un espace de noms défini ailleurs.</span><span class="sxs-lookup"><span data-stu-id="9f350-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="9f350-1178">Notez que l’espace de noms de l’élément **Schema** indiqué est `BooksModel` .</span><span class="sxs-lookup"><span data-stu-id="9f350-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="9f350-1179">La `Address` propriété sur l' `Publisher` **EntityType** est un type complexe qui est défini dans l' `ExtendedBooksModel` espace de noms (importé avec l’élément **using** ).</span><span class="sxs-lookup"><span data-stu-id="9f350-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="9f350-1180">Attributs d'annotation (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="9f350-1181">Les attributs d'annotation dans le langage CSDL (Conceptual Schema Definition Language) sont des attributs XML personnalisés dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="9f350-1182">En plus d'avoir une structure XML valide, les attributs d'annotation doivent satisfaire les critères suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="9f350-1183">Les attributs d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="9f350-1184">Plusieurs attributs d'annotation peuvent être appliqués à un élément CSDL donné.</span><span class="sxs-lookup"><span data-stu-id="9f350-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="9f350-1185">Les noms qualifiés complets de deux attributs d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="9f350-1186">Les attributs d'annotation peuvent être utilisés pour fournir des métadonnées supplémentaires sur des éléments dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="9f350-1187">Vous pouvez accéder aux métadonnées contenues dans les éléments d’annotation au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="9f350-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-1188">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1188">Example</span></span>

<span data-ttu-id="9f350-1189">L’exemple suivant montre un élément **EntityType** avec un attribut d’annotation (**CustomAttribute**).</span><span class="sxs-lookup"><span data-stu-id="9f350-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="9f350-1190">L'exemple fait également apparaître un élément d'annotation appliqué à l'élément de type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="9f350-1191">Le code suivant récupère les métadonnées dans l'attribut d'annotation et les écrit dans la console :</span><span class="sxs-lookup"><span data-stu-id="9f350-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="9f350-1192">Le code ci-dessus suppose que le fichier `School.csdl` se trouve dans le répertoire de sortie du projet et que vous avez ajouté les instructions `Imports` et `Using` suivantes à votre projet :</span><span class="sxs-lookup"><span data-stu-id="9f350-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="9f350-1193">Éléments d'annotation (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="9f350-1194">Les éléments d'annotation dans le langage CSDL (Conceptual Schema Definition Language) sont des éléments XML personnalisés dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="9f350-1195">En plus d'avoir une structure XML valide, les éléments d'annotation doivent satisfaire les critères suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="9f350-1196">Les éléments d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="9f350-1197">Plusieurs éléments d'annotation peuvent être des enfants d'un élément CSDL donné.</span><span class="sxs-lookup"><span data-stu-id="9f350-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="9f350-1198">Les noms qualifiés complets de deux éléments d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="9f350-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="9f350-1199">Les éléments d'annotation doivent apparaître après tous les autres éléments enfants d'un élément CSDL donné.</span><span class="sxs-lookup"><span data-stu-id="9f350-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="9f350-1200">Les éléments d'annotation permettent de fournir des métadonnées supplémentaires sur les éléments dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="9f350-1201">À partir de la .NET Framework version 4, les métadonnées contenues dans les éléments d’annotation sont accessibles au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="9f350-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-1202">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1202">Example</span></span>

<span data-ttu-id="9f350-1203">L’exemple suivant montre un élément **EntityType** avec un élément annotation (**customelement**).</span><span class="sxs-lookup"><span data-stu-id="9f350-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="9f350-1204">L'exemple fait également apparaître un attribut d'annotation appliqué à l'élément de type d'entité.</span><span class="sxs-lookup"><span data-stu-id="9f350-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="9f350-1205">Le code suivant récupère les métadonnées dans l'élément d'annotation et les écrit dans la console :</span><span class="sxs-lookup"><span data-stu-id="9f350-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="9f350-1206">Le code ci-dessus suppose que le fichier School.csdl se trouve dans le répertoire de sortie du projet et que vous avez ajouté les instructions `Imports` et `Using` suivantes à votre projet :</span><span class="sxs-lookup"><span data-stu-id="9f350-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="9f350-1207">Types de modèles conceptuels (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="9f350-1208">Le langage CSDL (Conceptual Schema Definition Language) prend en charge un ensemble de types de données primitifs abstraits, appelés **EDMSimpleTypes**, qui définissent des propriétés dans un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="9f350-1209">Les **EDMSimpleTypes** sont des proxys pour les types de données primitifs pris en charge dans l’environnement de stockage ou d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="9f350-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="9f350-1210">Le tableau suivant répertorie les types de données primitifs pris en charge par CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="9f350-1211">Le tableau répertorie également les facettes qui peuvent être appliquées à chaque **type EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="9f350-1212">Type EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="9f350-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="9f350-1213">Description</span><span class="sxs-lookup"><span data-stu-id="9f350-1213">Description</span></span>                                                | <span data-ttu-id="9f350-1214">Facettes applicables</span><span class="sxs-lookup"><span data-stu-id="9f350-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="9f350-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="9f350-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="9f350-1216">Contient des données binaires.</span><span class="sxs-lookup"><span data-stu-id="9f350-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="9f350-1217">MaxLength, FixedLength, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="9f350-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="9f350-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="9f350-1219">Contient la valeur **true** ou **false**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="9f350-1220">Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="9f350-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="9f350-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="9f350-1222">Contient une valeur d'entier 8 bits non signé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="9f350-1223">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="9f350-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="9f350-1225">Représente une date et une heure.</span><span class="sxs-lookup"><span data-stu-id="9f350-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="9f350-1226">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="9f350-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="9f350-1228">Contient une date et une heure en tant que décalage en minutes par rapport à l'heure GMT.</span><span class="sxs-lookup"><span data-stu-id="9f350-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="9f350-1229">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="9f350-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="9f350-1231">Contient une valeur numérique avec une précision et une échelle fixes.</span><span class="sxs-lookup"><span data-stu-id="9f350-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="9f350-1232">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="9f350-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="9f350-1234">Contient un nombre à virgule flottante avec une précision de 15 chiffres</span><span class="sxs-lookup"><span data-stu-id="9f350-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="9f350-1235">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1236">**EDM. float**</span><span class="sxs-lookup"><span data-stu-id="9f350-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="9f350-1237">Contient un nombre à virgule flottante avec une précision de 7 chiffres.</span><span class="sxs-lookup"><span data-stu-id="9f350-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="9f350-1238">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="9f350-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="9f350-1240">Contient un identificateur unique de 16 octets.</span><span class="sxs-lookup"><span data-stu-id="9f350-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="9f350-1241">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="9f350-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="9f350-1243">Contient une valeur d'entier 16 bits signé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="9f350-1244">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="9f350-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="9f350-1246">Contient une valeur d'entier 32 bits signé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="9f350-1247">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="9f350-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="9f350-1249">Contient une valeur d'entier 64 bits signé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="9f350-1250">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="9f350-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="9f350-1252">Contient une valeur d'entier 8 bits signé.</span><span class="sxs-lookup"><span data-stu-id="9f350-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="9f350-1253">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9f350-1254">**Edm.String**</span></span>                   | <span data-ttu-id="9f350-1255">Contient des données caractères.</span><span class="sxs-lookup"><span data-stu-id="9f350-1255">Contains character data.</span></span>                                   | <span data-ttu-id="9f350-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="9f350-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="9f350-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="9f350-1258">Contient une heure.</span><span class="sxs-lookup"><span data-stu-id="9f350-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="9f350-1259">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="9f350-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9f350-1260">**EDM. Geography**</span><span class="sxs-lookup"><span data-stu-id="9f350-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="9f350-1261">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="9f350-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="9f350-1263">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1264">**EDM. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="9f350-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="9f350-1265">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1266">**EDM. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="9f350-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="9f350-1267">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1268">**EDM. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="9f350-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="9f350-1269">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1270">**EDM. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="9f350-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="9f350-1271">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1272">**EDM. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="9f350-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="9f350-1273">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1274">**EDM. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="9f350-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="9f350-1275">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1276">**EDM. Geometry**</span><span class="sxs-lookup"><span data-stu-id="9f350-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="9f350-1277">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1278">**EDM. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="9f350-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="9f350-1279">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1280">**EDM. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="9f350-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="9f350-1281">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1282">**EDM. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="9f350-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="9f350-1283">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1284">**EDM. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="9f350-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="9f350-1285">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1286">**EDM. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="9f350-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="9f350-1287">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1288">**EDM. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="9f350-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="9f350-1289">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9f350-1290">**EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="9f350-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="9f350-1291">Nullable, par défaut, SRID</span><span class="sxs-lookup"><span data-stu-id="9f350-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="9f350-1292">Facettes (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9f350-1292">Facets (CSDL)</span></span>

<span data-ttu-id="9f350-1293">Les facettes dans le langage CSDL (Conceptual Schema Definition Language) représentent des contraintes sur les propriétés de types d'entités et de types complexes.</span><span class="sxs-lookup"><span data-stu-id="9f350-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="9f350-1294">Les facettes apparaissent comme des attributs XML sur les éléments CSDL suivants :</span><span class="sxs-lookup"><span data-stu-id="9f350-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="9f350-1295">Propriété</span><span class="sxs-lookup"><span data-stu-id="9f350-1295">Property</span></span>
-   <span data-ttu-id="9f350-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="9f350-1296">TypeRef</span></span>
-   <span data-ttu-id="9f350-1297">Paramètre</span><span class="sxs-lookup"><span data-stu-id="9f350-1297">Parameter</span></span>

<span data-ttu-id="9f350-1298">Le tableau ci-dessous décrit les facettes prises en charge dans le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="9f350-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="9f350-1299">Toutes les facettes sont facultatives.</span><span class="sxs-lookup"><span data-stu-id="9f350-1299">All facets are optional.</span></span> <span data-ttu-id="9f350-1300">Certaines facettes répertoriées ci-dessous sont utilisées par la Entity Framework lors de la génération d’une base de données à partir d’un modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="9f350-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="9f350-1301">Pour plus d’informations sur les types de données dans un modèle conceptuel, consultez types de modèles conceptuels (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9f350-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="9f350-1302">Facette</span><span class="sxs-lookup"><span data-stu-id="9f350-1302">Facet</span></span>               | <span data-ttu-id="9f350-1303">Description</span><span class="sxs-lookup"><span data-stu-id="9f350-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="9f350-1304">S’applique à</span><span class="sxs-lookup"><span data-stu-id="9f350-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="9f350-1305">Utilisée pour la génération de base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-1305">Used for the database generation</span></span> | <span data-ttu-id="9f350-1306">Utilisée par le runtime.</span><span class="sxs-lookup"><span data-stu-id="9f350-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="9f350-1307">**Classement**</span><span class="sxs-lookup"><span data-stu-id="9f350-1307">**Collation**</span></span>       | <span data-ttu-id="9f350-1308">Spécifie la table de classement ou ordre de tri à utiliser lors de l'exécution d'opérations de comparaison et de tri sur des valeurs de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="9f350-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9f350-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9f350-1310">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1310">Yes</span></span>                              | <span data-ttu-id="9f350-1311">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1311">No</span></span>                  |
| <span data-ttu-id="9f350-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="9f350-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="9f350-1313">Indique que la valeur de la propriété doit être utilisée pour des contrôles d'accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="9f350-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="9f350-1314">Toutes les propriétés **type EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="9f350-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9f350-1315">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1315">No</span></span>                               | <span data-ttu-id="9f350-1316">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1316">Yes</span></span>                 |
| <span data-ttu-id="9f350-1317">**Par défaut**</span><span class="sxs-lookup"><span data-stu-id="9f350-1317">**Default**</span></span>         | <span data-ttu-id="9f350-1318">Spécifie la valeur par défaut de la propriété si aucune valeur n'est fournie en cas d'instanciation.</span><span class="sxs-lookup"><span data-stu-id="9f350-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="9f350-1319">Toutes les propriétés **type EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="9f350-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9f350-1320">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1320">Yes</span></span>                              | <span data-ttu-id="9f350-1321">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1321">Yes</span></span>                 |
| <span data-ttu-id="9f350-1322">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="9f350-1322">**FixedLength**</span></span>     | <span data-ttu-id="9f350-1323">Spécifie si la longueur de la valeur de propriété peut varier.</span><span class="sxs-lookup"><span data-stu-id="9f350-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="9f350-1324">**Edm. Binary**, **Edm. String**</span><span class="sxs-lookup"><span data-stu-id="9f350-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9f350-1325">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1325">Yes</span></span>                              | <span data-ttu-id="9f350-1326">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1326">No</span></span>                  |
| <span data-ttu-id="9f350-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9f350-1327">**MaxLength**</span></span>       | <span data-ttu-id="9f350-1328">Spécifie la longueur maximale de la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="9f350-1329">**Edm. Binary**, **Edm. String**</span><span class="sxs-lookup"><span data-stu-id="9f350-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9f350-1330">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1330">Yes</span></span>                              | <span data-ttu-id="9f350-1331">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1331">No</span></span>                  |
| <span data-ttu-id="9f350-1332">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="9f350-1332">**Nullable**</span></span>        | <span data-ttu-id="9f350-1333">Spécifie si la propriété peut avoir une valeur **null** .</span><span class="sxs-lookup"><span data-stu-id="9f350-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="9f350-1334">Toutes les propriétés **type EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="9f350-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9f350-1335">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1335">Yes</span></span>                              | <span data-ttu-id="9f350-1336">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1336">Yes</span></span>                 |
| <span data-ttu-id="9f350-1337">**Précision**</span><span class="sxs-lookup"><span data-stu-id="9f350-1337">**Precision**</span></span>       | <span data-ttu-id="9f350-1338">Pour les propriétés de type **Decimal**, spécifie le nombre de chiffres qu’une valeur de propriété peut avoir.</span><span class="sxs-lookup"><span data-stu-id="9f350-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="9f350-1339">Pour les propriétés de type **Time**, **DateTime**et **DateTimeOffset**, spécifie le nombre de chiffres pour la partie fractionnaire des secondes de la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="9f350-1340">**Edm. DateTime**, **Edm. DateTimeOffset**, **Edm. Decimal**, **Edm. Time**</span><span class="sxs-lookup"><span data-stu-id="9f350-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="9f350-1341">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1341">Yes</span></span>                              | <span data-ttu-id="9f350-1342">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1342">No</span></span>                  |
| <span data-ttu-id="9f350-1343">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="9f350-1343">**Scale**</span></span>           | <span data-ttu-id="9f350-1344">Spécifie le nombre de chiffres à droite de la virgule décimale pour la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="9f350-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="9f350-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="9f350-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="9f350-1346">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1346">Yes</span></span>                              | <span data-ttu-id="9f350-1347">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1347">No</span></span>                  |
| <span data-ttu-id="9f350-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9f350-1348">**SRID**</span></span>            | <span data-ttu-id="9f350-1349">Spécifie l’ID du système de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="9f350-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="9f350-1350">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f350-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="9f350-1351">**EDM. Geography, EDM. GeographyPoint, EDM. GeographyLineString, EDM. GeographyPolygon, EDM. GeographyMultiPoint, EDM. GeographyMultiLineString, EDM. GeographyMultiPolygon, EDM. GeographyCollection, EDM. Geometry, EDM. GeometryPoint, EDM. GeometryLineString, EDM. GeometryPolygon, EDM. GeometryMultiPoint, EDM. GeometryMultiLineString, EDM. GeometryMultiPolygon, EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="9f350-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="9f350-1352">Non</span><span class="sxs-lookup"><span data-stu-id="9f350-1352">No</span></span>                               | <span data-ttu-id="9f350-1353">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1353">Yes</span></span>                 |
| <span data-ttu-id="9f350-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9f350-1354">**Unicode**</span></span>         | <span data-ttu-id="9f350-1355">Indique si la valeur de propriété est stockée au format Unicode.</span><span class="sxs-lookup"><span data-stu-id="9f350-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="9f350-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9f350-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9f350-1357">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1357">Yes</span></span>                              | <span data-ttu-id="9f350-1358">Oui</span><span class="sxs-lookup"><span data-stu-id="9f350-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="9f350-1359">Lors de la génération d’une base de données à partir d’un modèle conceptuel, l’Assistant génération de base de données reconnaît la valeur de l’attribut **StoreGeneratedPattern** sur un élément de **propriété** s’il se trouve dans l’espace de noms suivant : https://schemas.microsoft.com/ado/2009/02/edm/annotation .</span><span class="sxs-lookup"><span data-stu-id="9f350-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="9f350-1360">Les valeurs prises en charge pour l’attribut sont **Identity** et **computeed**.</span><span class="sxs-lookup"><span data-stu-id="9f350-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="9f350-1361">La valeur **Identity** produit une colonne de base de données avec une valeur d’identité générée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="9f350-1362">Une valeur **calculée** génère une colonne avec une valeur qui est calculée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9f350-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="9f350-1363">Exemple</span><span class="sxs-lookup"><span data-stu-id="9f350-1363">Example</span></span>

<span data-ttu-id="9f350-1364">L'exemple suivant illustre l'application de facettes aux propriétés d'un type d'entité :</span><span class="sxs-lookup"><span data-stu-id="9f350-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
