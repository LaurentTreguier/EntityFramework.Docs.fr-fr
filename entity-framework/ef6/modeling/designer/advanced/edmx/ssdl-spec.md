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
# <a name="ssdl-specification"></a><span data-ttu-id="a19ba-103">Spécification SSDL</span><span class="sxs-lookup"><span data-stu-id="a19ba-103">SSDL Specification</span></span>
<span data-ttu-id="a19ba-104">SSDL (Store Schema Definition Language) est un langage basé sur XML qui décrit le modèle de stockage d'une application Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a19ba-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="a19ba-105">Dans une application Entity Framework, les métadonnées du modèle de stockage sont chargées à partir d’un fichier. SSDL (écrit en SSDL) dans une instance de System. Data. Metadata. Edm. StoreItemCollection et sont accessibles à l’aide des méthodes de la classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="a19ba-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="a19ba-106">Entity Framework utilise les métadonnées du modèle de stockage pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques au stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="a19ba-107">Le Entity Framework Designer (concepteur EF) stocke les informations de modèle de stockage dans un fichier. edmx au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="a19ba-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="a19ba-108">Au moment de la génération, le Entity Designer utilise les informations d’un fichier. edmx pour créer le fichier. ssdl requis par Entity Framework au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="a19ba-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="a19ba-109">Les versions de SSDL sont différenciées par les espaces de noms XML.</span><span class="sxs-lookup"><span data-stu-id="a19ba-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="a19ba-110">Version SSDL</span><span class="sxs-lookup"><span data-stu-id="a19ba-110">SSDL Version</span></span> | <span data-ttu-id="a19ba-111">Espace de noms XML</span><span class="sxs-lookup"><span data-stu-id="a19ba-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="a19ba-112">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="a19ba-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="a19ba-113">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="a19ba-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="a19ba-114">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="a19ba-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="a19ba-115">Association, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-115">Association Element (SSDL)</span></span>

<span data-ttu-id="a19ba-116">Un élément **Association** en Store Schema Definition Language (SSDL) spécifie des colonnes de table qui participent à une contrainte de clé étrangère dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="a19ba-117">Deux éléments End enfants obligatoires spécifient des tables aux terminaisons de l'association et la multiplicité à chaque terminaison.</span><span class="sxs-lookup"><span data-stu-id="a19ba-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="a19ba-118">Un élément ReferentialConstraint facultatif spécifie les terminaisons principales et dépendantes de l'association ainsi que les colonnes participantes.</span><span class="sxs-lookup"><span data-stu-id="a19ba-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="a19ba-119">Si aucun élément **ReferentialConstraint** n’est présent, un élément AssociationSetMapping doit être utilisé pour spécifier les mappages de colonnes pour l’Association.</span><span class="sxs-lookup"><span data-stu-id="a19ba-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="a19ba-120">L’élément **Association** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-121">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-122">End (exactement deux éléments) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-122">End (exactly two)</span></span>
-   <span data-ttu-id="a19ba-123">ReferentialConstraint (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="a19ba-124">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-125">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-125">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-126">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="a19ba-127">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-127">Attribute Name</span></span> | <span data-ttu-id="a19ba-128">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-128">Is Required</span></span> | <span data-ttu-id="a19ba-129">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-130">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-130">**Name**</span></span>       | <span data-ttu-id="a19ba-131">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-131">Yes</span></span>         | <span data-ttu-id="a19ba-132">Nom de la contrainte de clé étrangère correspondante dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-133">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="a19ba-134">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-135">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-136">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-136">Example</span></span>

<span data-ttu-id="a19ba-137">L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère \*\*FK \_ \*\* de la clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="a19ba-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="a19ba-138">AssociationSet, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="a19ba-139">L’élément **AssociationSet** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère entre deux tables dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="a19ba-140">Les colonnes de la table qui participent à la contrainte de clé étrangère sont spécifiées dans un élément Association.</span><span class="sxs-lookup"><span data-stu-id="a19ba-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="a19ba-141">L’élément **Association** qui correspond à un élément **AssociationSet** donné est spécifié dans l’attribut **Association** de l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="a19ba-142">Les ensembles d'associations SSDL sont mappés aux ensembles d'associations CSDL par un élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="a19ba-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="a19ba-143">Toutefois, si l’Association CSDL pour un ensemble d’associations CSDL donné est définie à l’aide d’un élément ReferentialConstraint, aucun élément **AssociationSetMapping** correspondant n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="a19ba-144">Dans ce cas, si un élément **AssociationSetMapping** est présent, les mappages qu’il définit seront remplacés par l’élément **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="a19ba-145">L’élément **AssociationSet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-146">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-147">End (zéro ou deux éléments) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-147">End (zero or two)</span></span>
-   <span data-ttu-id="a19ba-148">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-149">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-149">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-150">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="a19ba-151">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-151">Attribute Name</span></span>  | <span data-ttu-id="a19ba-152">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-152">Is Required</span></span> | <span data-ttu-id="a19ba-153">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-154">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-154">**Name**</span></span>        | <span data-ttu-id="a19ba-155">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-155">Yes</span></span>         | <span data-ttu-id="a19ba-156">Nom de la contrainte de clé étrangère que l'ensemble d'associations représente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="a19ba-157">**Association**</span><span class="sxs-lookup"><span data-stu-id="a19ba-157">**Association**</span></span> | <span data-ttu-id="a19ba-158">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-158">Yes</span></span>         | <span data-ttu-id="a19ba-159">Nom de l'association qui définit les colonnes qui participent à la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-160">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="a19ba-161">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-162">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-163">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-163">Example</span></span>

<span data-ttu-id="a19ba-164">L’exemple suivant montre un élément **AssociationSet** qui représente la `FK_CustomerOrders` contrainte de clé étrangère dans la base de données sous-jacente :</span><span class="sxs-lookup"><span data-stu-id="a19ba-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="a19ba-165">Élément CollectionType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="a19ba-166">L’élément **CollectionType** en Store Schema Definition Language (SSDL) spécifie que le type de retour d’une fonction est une collection.</span><span class="sxs-lookup"><span data-stu-id="a19ba-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="a19ba-167">L’élément **CollectionType** est un enfant de l’élément ReturnType.</span><span class="sxs-lookup"><span data-stu-id="a19ba-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="a19ba-168">Le type de collection est spécifié à l’aide de l’élément enfant RowType :</span><span class="sxs-lookup"><span data-stu-id="a19ba-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="a19ba-169">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="a19ba-170">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-171">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-172">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-172">Example</span></span>

<span data-ttu-id="a19ba-173">L’exemple suivant montre une fonction qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes.</span><span class="sxs-lookup"><span data-stu-id="a19ba-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="a19ba-174">CommandText, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="a19ba-175">L’élément **CommandText** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui vous permet de définir une instruction SQL qui est exécutée au niveau de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="a19ba-176">L’élément **CommandText** vous permet d’ajouter des fonctionnalités qui sont similaires à une procédure stockée dans la base de données, mais vous définissez l’élément **CommandText** dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="a19ba-177">L’élément **CommandText** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="a19ba-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="a19ba-178">Le corps de l’élément **CommandText** doit être une instruction SQL valide pour la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="a19ba-179">Aucun attribut n’est applicable à l’élément **CommandText** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-180">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-180">Example</span></span>

<span data-ttu-id="a19ba-181">L’exemple suivant montre un élément **Function** avec un élément **CommandText** enfant.</span><span class="sxs-lookup"><span data-stu-id="a19ba-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="a19ba-182">Exposez la fonction **UpdateProductInOrder** en tant que méthode sur ObjectContext en l’important dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="a19ba-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="a19ba-183">DefiningQuery, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="a19ba-184">L’élément **DefiningQuery** en Store Schema Definition Language (SSDL) vous permet d’exécuter une instruction SQL directement dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="a19ba-185">L’élément **DefiningQuery** est couramment utilisé comme une vue de base de données, mais la vue est définie dans le modèle de stockage au lieu de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="a19ba-186">La vue définie dans un élément **DefiningQuery** peut être mappée à un type d’entité dans le modèle conceptuel via un élément EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="a19ba-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="a19ba-187">Ces mappages sont en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="a19ba-187">These mappings are read-only.</span></span>  

<span data-ttu-id="a19ba-188">La syntaxe SSDL suivante illustre la déclaration d’un **EntitySet** suivi de l’élément **DefiningQuery** qui contient une requête utilisée pour récupérer la vue.</span><span class="sxs-lookup"><span data-stu-id="a19ba-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="a19ba-189">Vous pouvez utiliser des procédures stockées dans le Entity Framework pour activer des scénarios de lecture-écriture sur des vues.</span><span class="sxs-lookup"><span data-stu-id="a19ba-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="a19ba-190">Vous pouvez utiliser une vue de source de données ou une vue de Entity SQL comme table de base pour récupérer des données et pour le traitement des modifications par des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="a19ba-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="a19ba-191">Vous pouvez utiliser l’élément **DefiningQuery** pour cibler Microsoft SQL Server Compact 3,5.</span><span class="sxs-lookup"><span data-stu-id="a19ba-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="a19ba-192">Bien que SQL Server Compact 3,5 ne prenne pas en charge les procédures stockées, vous pouvez implémenter des fonctionnalités similaires avec l’élément **DefiningQuery** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="a19ba-193">Cet élément peut s'avérer également utile pour créer des procédures stockées afin de surmonter une incompatibilité entre les types de données utilisés dans le langage de programmation et ceux de la source de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="a19ba-194">Vous pouvez écrire un **DefiningQuery** qui accepte un certain ensemble de paramètres, puis appelle une procédure stockée avec un jeu de paramètres différent, par exemple, une procédure stockée qui supprime des données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="a19ba-195">Élément Dependent (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="a19ba-196">L’élément **dépendant** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison dépendante d’une contrainte de clé étrangère (également appelée contrainte référentielle).</span><span class="sxs-lookup"><span data-stu-id="a19ba-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="a19ba-197">L’élément **dépendant** spécifie la ou les colonnes d’une table qui font référence à une ou plusieurs colonnes de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="a19ba-198">Les éléments **PropertyRef** spécifient les colonnes qui sont référencées.</span><span class="sxs-lookup"><span data-stu-id="a19ba-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="a19ba-199">L’élément principal spécifie les colonnes de clés primaires référencées par les colonnes spécifiées dans l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="a19ba-200">L’élément **dépendant** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-201">PropertyRef (un ou plusieurs) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="a19ba-202">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-203">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-203">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-204">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="a19ba-205">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-205">Attribute Name</span></span> | <span data-ttu-id="a19ba-206">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-206">Is Required</span></span> | <span data-ttu-id="a19ba-207">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-208">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-208">**Role**</span></span>       | <span data-ttu-id="a19ba-209">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-209">Yes</span></span>         | <span data-ttu-id="a19ba-210">La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; dans le cas contraire, il s’agit du nom de la table qui contient la colonne de référence.</span><span class="sxs-lookup"><span data-stu-id="a19ba-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-211">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="a19ba-212">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-213">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-214">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-214">Example</span></span>

<span data-ttu-id="a19ba-215">L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère \*\*FK \_ \*\* .</span><span class="sxs-lookup"><span data-stu-id="a19ba-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="a19ba-216">L’élément **dépendant** spécifie la colonne **CustomerID** de la table **Order** comme terminaison dépendante de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="a19ba-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="a19ba-217">Documentation, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="a19ba-218">L’élément **documentation** en Store Schema Definition Language (SSDL) peut être utilisé pour fournir des informations sur un objet défini dans un élément parent.</span><span class="sxs-lookup"><span data-stu-id="a19ba-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="a19ba-219">L’élément **documentation** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-220">**Summary**: brève description de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="a19ba-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="a19ba-221">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="a19ba-221">(zero or one element)</span></span>
-   <span data-ttu-id="a19ba-222">**LongDescription**: description complète de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="a19ba-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="a19ba-223">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="a19ba-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-224">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-224">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-225">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **documentation** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="a19ba-226">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-227">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-228">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-228">Example</span></span>

<span data-ttu-id="a19ba-229">L’exemple suivant montre l’élément de **documentation** en tant qu’élément enfant d’un élément EntityType.</span><span class="sxs-lookup"><span data-stu-id="a19ba-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="a19ba-230">End, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-230">End Element (SSDL)</span></span>

<span data-ttu-id="a19ba-231">L’élément **end** en Store Schema Definition Language (SSDL) spécifie la table et le nombre de lignes à une terminaison d’une contrainte FOREIGN KEY dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="a19ba-232">L’élément **end** peut être un enfant de l’élément Association ou de l’élément AssociationSet.</span><span class="sxs-lookup"><span data-stu-id="a19ba-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="a19ba-233">Dans chaque cas, les éléments enfants et les attributs applicables possibles sont différents.</span><span class="sxs-lookup"><span data-stu-id="a19ba-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="a19ba-234">Élément End comme enfant de l'élément Association</span><span class="sxs-lookup"><span data-stu-id="a19ba-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="a19ba-235">Un élément **end** (en tant qu’enfant de l’élément **Association** ) spécifie la table et le nombre de lignes à la fin d’une contrainte de clé étrangère, respectivement avec les attributs **type** et **Multiplicity** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="a19ba-236">Les terminaisons d'une contrainte de clé étrangère sont définies dans le cadre d'un ensemble d'associations SSDL ; un ensemble d'associations SSDL doit avoir exactement deux terminaisons.</span><span class="sxs-lookup"><span data-stu-id="a19ba-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="a19ba-237">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-238">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-239">OnDelete (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-240">éléments d'annotation (zéro, un ou plusieurs éléments).</span><span class="sxs-lookup"><span data-stu-id="a19ba-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-241">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-241">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-242">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **final** lorsqu’il est l’enfant d’un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="a19ba-243">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-243">Attribute Name</span></span>   | <span data-ttu-id="a19ba-244">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-244">Is Required</span></span> | <span data-ttu-id="a19ba-245">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-246">**Type**</span><span class="sxs-lookup"><span data-stu-id="a19ba-246">**Type**</span></span>         | <span data-ttu-id="a19ba-247">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-247">Yes</span></span>         | <span data-ttu-id="a19ba-248">Nom complet du jeu d'entités SSDL qui est à la terminaison de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="a19ba-249">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-249">**Role**</span></span>         | <span data-ttu-id="a19ba-250">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-250">No</span></span>          | <span data-ttu-id="a19ba-251">Valeur de l’attribut **role** dans l’élément principal ou dépendant de l’élément ReferentialConstraint correspondant (s’il est utilisé).</span><span class="sxs-lookup"><span data-stu-id="a19ba-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="a19ba-252">**Multiplicité**</span><span class="sxs-lookup"><span data-stu-id="a19ba-252">**Multiplicity**</span></span> | <span data-ttu-id="a19ba-253">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-253">Yes</span></span>         | <span data-ttu-id="a19ba-254">**1**, **0.. 1**, ou **\*** selon le nombre de lignes qui peuvent être à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="a19ba-255">**1** indique qu’une seule ligne existe à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="a19ba-256">**0.. 1** indique qu’il existe zéro ou une ligne à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="a19ba-257">**\*** indique que zéro, une ou plusieurs lignes existent à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-258">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="a19ba-259">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-260">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="a19ba-261">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-261">Example</span></span>

<span data-ttu-id="a19ba-262">L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère \*\*FK \_ \*\* .</span><span class="sxs-lookup"><span data-stu-id="a19ba-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="a19ba-263">Les valeurs de **multiplicité** spécifiées sur chaque élément de **fin** indiquent que de nombreuses lignes de la table **Orders** peuvent être associées à une ligne de la table **Customers** , mais qu’une seule ligne de la table **Customers** peut être associée à une ligne dans la table **Orders** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="a19ba-264">En outre, l’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="a19ba-265">Élément End comme enfant de l'élément AssociationSet</span><span class="sxs-lookup"><span data-stu-id="a19ba-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="a19ba-266">L’élément **end** (en tant qu’enfant de l’élément **AssociationSet** ) spécifie une table à une terminaison d’une contrainte de clé étrangère dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="a19ba-267">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-268">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-269">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-270">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-270">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-271">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **end** lorsqu’il est l’enfant d’un élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="a19ba-272">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-272">Attribute Name</span></span> | <span data-ttu-id="a19ba-273">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-273">Is Required</span></span> | <span data-ttu-id="a19ba-274">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-275">**EntitySet ;**</span><span class="sxs-lookup"><span data-stu-id="a19ba-275">**EntitySet**</span></span>  | <span data-ttu-id="a19ba-276">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-276">Yes</span></span>         | <span data-ttu-id="a19ba-277">Nom du jeu d'entités SSDL qui est à la terminaison de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="a19ba-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="a19ba-278">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-278">**Role**</span></span>       | <span data-ttu-id="a19ba-279">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-279">No</span></span>          | <span data-ttu-id="a19ba-280">Valeur de l’un des attributs de **rôle** spécifiés sur un élément de **fin** de l’élément Association correspondant.</span><span class="sxs-lookup"><span data-stu-id="a19ba-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-281">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="a19ba-282">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-283">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="a19ba-284">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-284">Example</span></span>

<span data-ttu-id="a19ba-285">L’exemple suivant montre un élément **EntityContainer** avec un élément **AssociationSet** avec deux éléments **end** :</span><span class="sxs-lookup"><span data-stu-id="a19ba-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="a19ba-286">EntityContainer, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="a19ba-287">Un élément **EntityContainer** en Store Schema Definition Language (SSDL) décrit la structure de la source de données sous-jacente dans une application Entity Framework : les jeux d’entités SSDL (définis dans les éléments EntitySet) représentent les tables d’une base de données, les types d’entités SSDL (définis dans les éléments EntityType) représentent les lignes d’une table, et les ensembles d’associations (définis dans les éléments AssociationSet) représentent des contraintes</span><span class="sxs-lookup"><span data-stu-id="a19ba-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="a19ba-288">Un conteneur d'entités de modèle de stockage est mappé à un conteneur d'entités de modèle conceptuel via l'élément EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="a19ba-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="a19ba-289">Un élément **EntityContainer** peut avoir zéro ou un élément documentation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="a19ba-290">Si un élément de **documentation** est présent, il doit précéder tous les autres éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="a19ba-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="a19ba-291">Un élément **EntityContainer** peut avoir zéro ou plusieurs des éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-292">EntitySet ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-292">EntitySet</span></span>
-   <span data-ttu-id="a19ba-293">AssociationSet ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-293">AssociationSet</span></span>
-   <span data-ttu-id="a19ba-294">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-295">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-295">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-296">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="a19ba-297">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-297">Attribute Name</span></span> | <span data-ttu-id="a19ba-298">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-298">Is Required</span></span> | <span data-ttu-id="a19ba-299">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-300">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-300">**Name**</span></span>       | <span data-ttu-id="a19ba-301">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-301">Yes</span></span>         | <span data-ttu-id="a19ba-302">Nom du conteneur d'entités.</span><span class="sxs-lookup"><span data-stu-id="a19ba-302">The name of the entity container.</span></span> <span data-ttu-id="a19ba-303">Ce nom ne peut pas contenir de point (.).</span><span class="sxs-lookup"><span data-stu-id="a19ba-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-304">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="a19ba-305">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-306">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-307">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-307">Example</span></span>

<span data-ttu-id="a19ba-308">L’exemple suivant montre un élément **EntityContainer** qui définit deux jeux d’entités et un ensemble d’associations.</span><span class="sxs-lookup"><span data-stu-id="a19ba-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="a19ba-309">Notez que les noms de type d'entité et de type d'association sont qualifiés par le nom de l'espace de noms du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="a19ba-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="a19ba-310">EntitySet, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="a19ba-311">Un élément **EntitySet** en Store Schema Definition Language (SSDL) représente une table ou une vue dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="a19ba-312">Un élément EntityType en SSDL représente une ligne dans la table ou la vue.</span><span class="sxs-lookup"><span data-stu-id="a19ba-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="a19ba-313">L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="a19ba-314">Le mappage entre un jeu d'entités CSDL et un jeu d'entités SSDL est spécifié dans un élément EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="a19ba-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="a19ba-315">L’élément **EntitySet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-316">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-317">DefiningQuery (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-318">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-319">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-319">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-320">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="a19ba-321">Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="a19ba-322">Ces attributs sont utilisés par l'Assistant Mise à jour du modèle lors de la mise à jour d'un modèle.</span><span class="sxs-lookup"><span data-stu-id="a19ba-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="a19ba-323">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-323">Attribute Name</span></span> | <span data-ttu-id="a19ba-324">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-324">Is Required</span></span> | <span data-ttu-id="a19ba-325">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-326">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-326">**Name**</span></span>       | <span data-ttu-id="a19ba-327">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-327">Yes</span></span>         | <span data-ttu-id="a19ba-328">Nom du jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="a19ba-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="a19ba-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="a19ba-329">**EntityType**</span></span> | <span data-ttu-id="a19ba-330">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-330">Yes</span></span>         | <span data-ttu-id="a19ba-331">Nom qualifié complet du type d'entité pour lequel le jeu d'entités contient des instances.</span><span class="sxs-lookup"><span data-stu-id="a19ba-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="a19ba-332">**Schéma**</span><span class="sxs-lookup"><span data-stu-id="a19ba-332">**Schema**</span></span>     | <span data-ttu-id="a19ba-333">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-333">No</span></span>          | <span data-ttu-id="a19ba-334">Schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="a19ba-335">**Table**</span><span class="sxs-lookup"><span data-stu-id="a19ba-335">**Table**</span></span>      | <span data-ttu-id="a19ba-336">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-336">No</span></span>          | <span data-ttu-id="a19ba-337">Table de base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="a19ba-338">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="a19ba-339">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-340">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-341">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-341">Example</span></span>

<span data-ttu-id="a19ba-342">L’exemple suivant montre un élément **EntityContainer** qui a deux éléments **EntitySet** et un élément **AssociationSet** :</span><span class="sxs-lookup"><span data-stu-id="a19ba-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="a19ba-343">EntityType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="a19ba-344">Un élément **EntityType** en Store Schema Definition Language (SSDL) représente une ligne dans une table ou une vue de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="a19ba-345">Un élément EntitySet en SSDL représente la table ou la vue dans laquelle les lignes résultent.</span><span class="sxs-lookup"><span data-stu-id="a19ba-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="a19ba-346">L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="a19ba-347">Le mappage entre un type d'entité SSDL et un type d'entité CSDL est spécifié dans un élément EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="a19ba-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="a19ba-348">L’élément **EntityType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-349">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-350">Key (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="a19ba-351">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-352">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-352">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-353">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="a19ba-354">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-354">Attribute Name</span></span> | <span data-ttu-id="a19ba-355">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-355">Is Required</span></span> | <span data-ttu-id="a19ba-356">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-357">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-357">**Name**</span></span>       | <span data-ttu-id="a19ba-358">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-358">Yes</span></span>         | <span data-ttu-id="a19ba-359">Le nom du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="a19ba-359">The name of the entity type.</span></span> <span data-ttu-id="a19ba-360">Cette valeur est habituellement la même que le nom de la table dans laquelle le type d'entité représente une ligne.</span><span class="sxs-lookup"><span data-stu-id="a19ba-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="a19ba-361">Cette valeur ne peut pas contenir de point (.).</span><span class="sxs-lookup"><span data-stu-id="a19ba-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-362">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="a19ba-363">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-364">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-365">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-365">Example</span></span>

<span data-ttu-id="a19ba-366">L’exemple suivant montre un élément **EntityType** avec deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="a19ba-366">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="a19ba-367">Function, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-367">Function Element (SSDL)</span></span>

<span data-ttu-id="a19ba-368">L’élément **Function** de Store Schema Definition Language (SSDL) spécifie une procédure stockée qui existe dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="a19ba-369">L’élément **Function** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-370">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-371">Paramètre (zéro, un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="a19ba-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="a19ba-372">CommandText (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="a19ba-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="a19ba-373">ReturnType (zéro, un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="a19ba-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="a19ba-374">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="a19ba-375">Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** ou l’attribut **ReturnType** (voir ci-dessous), mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="a19ba-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="a19ba-376">Les procédures stockées spécifiées dans le modèle de stockage peuvent être importées dans le modèle conceptuel d'une application.</span><span class="sxs-lookup"><span data-stu-id="a19ba-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="a19ba-377">Pour plus d’informations, consultez [interrogation avec des procédures stockées](xref:ef6/modeling/designer/stored-procedures/query).</span><span class="sxs-lookup"><span data-stu-id="a19ba-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="a19ba-378">L’élément **Function** peut également être utilisé pour définir des fonctions personnalisées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-379">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-379">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-380">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Function** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="a19ba-381">Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="a19ba-382">Ces attributs sont utilisés par l'Assistant Mise à jour du modèle lors de la mise à jour d'un modèle.</span><span class="sxs-lookup"><span data-stu-id="a19ba-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="a19ba-383">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-383">Attribute Name</span></span>             | <span data-ttu-id="a19ba-384">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-384">Is Required</span></span> | <span data-ttu-id="a19ba-385">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-386">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-386">**Name**</span></span>                   | <span data-ttu-id="a19ba-387">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-387">Yes</span></span>         | <span data-ttu-id="a19ba-388">Nom de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="a19ba-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="a19ba-389">**ReturnType**</span></span>             | <span data-ttu-id="a19ba-390">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-390">No</span></span>          | <span data-ttu-id="a19ba-391">Type de retour de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="a19ba-392">**Agrégat**</span><span class="sxs-lookup"><span data-stu-id="a19ba-392">**Aggregate**</span></span>              | <span data-ttu-id="a19ba-393">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-393">No</span></span>          | <span data-ttu-id="a19ba-394">**True** si la procédure stockée retourne une valeur d’agrégation ; Sinon, **false**.</span><span class="sxs-lookup"><span data-stu-id="a19ba-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="a19ba-395">**Intégrée**</span><span class="sxs-lookup"><span data-stu-id="a19ba-395">**BuiltIn**</span></span>                | <span data-ttu-id="a19ba-396">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-396">No</span></span>          | <span data-ttu-id="a19ba-397">**True** si la fonction est une fonction intégrée<sup>1</sup> ; Sinon, **false**.</span><span class="sxs-lookup"><span data-stu-id="a19ba-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="a19ba-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="a19ba-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="a19ba-399">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-399">No</span></span>          | <span data-ttu-id="a19ba-400">Nom de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="a19ba-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="a19ba-401">**NiladicFunction**</span></span>        | <span data-ttu-id="a19ba-402">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-402">No</span></span>          | <span data-ttu-id="a19ba-403">**True** si la fonction est une fonction niladic<sup>2</sup> ; **False** dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="a19ba-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="a19ba-404">**IsComposable**</span></span>           | <span data-ttu-id="a19ba-405">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-405">No</span></span>          | <span data-ttu-id="a19ba-406">**True** si la fonction est une fonction composable<sup>3</sup> ; **False** dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="a19ba-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="a19ba-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="a19ba-408">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-408">No</span></span>          | <span data-ttu-id="a19ba-409">Énumération qui définit la sémantique de type utilisée pour résoudre les surcharges de fonction.</span><span class="sxs-lookup"><span data-stu-id="a19ba-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="a19ba-410">L'énumération est définie dans le manifeste du fournisseur par définition de fonction.</span><span class="sxs-lookup"><span data-stu-id="a19ba-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="a19ba-411">La valeur par défaut est **AllowImplicitConversion**.</span><span class="sxs-lookup"><span data-stu-id="a19ba-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="a19ba-412">**Schéma**</span><span class="sxs-lookup"><span data-stu-id="a19ba-412">**Schema**</span></span>                 | <span data-ttu-id="a19ba-413">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-413">No</span></span>          | <span data-ttu-id="a19ba-414">Nom du schéma dans lequel une procédure stockée est définie.</span><span class="sxs-lookup"><span data-stu-id="a19ba-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="a19ba-415"><sup>1</sup> une fonction intégrée est une fonction définie dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="a19ba-416">Pour plus d’informations sur les fonctions définies dans le modèle de stockage, consultez CommandText, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="a19ba-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="a19ba-417"><sup>2</sup> une fonction niladic est une fonction qui n’accepte aucun paramètre et, lorsqu’elle est appelée, elle ne requiert pas de parenthèses.</span><span class="sxs-lookup"><span data-stu-id="a19ba-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="a19ba-418"><sup>3</sup> deux fonctions sont composables si la sortie d’une fonction peut être l’entrée de l’autre fonction.</span><span class="sxs-lookup"><span data-stu-id="a19ba-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="a19ba-419">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fonction** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="a19ba-420">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-421">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-422">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-422">Example</span></span>

<span data-ttu-id="a19ba-423">L’exemple suivant montre un élément **Function** qui correspond à la procédure stockée **UpdateOrderQuantity** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="a19ba-424">La procédure stockée accepte deux paramètres et ne retourne pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="a19ba-424">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="a19ba-425">Key, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-425">Key Element (SSDL)</span></span>

<span data-ttu-id="a19ba-426">L’élément **clé** en Store Schema Definition Language (SSDL) représente la clé primaire d’une table dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="a19ba-427">**Key** est un élément enfant d’un élément EntityType, qui représente une ligne dans une table.</span><span class="sxs-lookup"><span data-stu-id="a19ba-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="a19ba-428">La clé primaire est définie dans l’élément **Key** en référençant un ou plusieurs éléments Property définis sur l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="a19ba-429">L’élément **Key** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-430">PropertyRef (un ou plusieurs) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="a19ba-431">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-431">Annotation elements</span></span>

<span data-ttu-id="a19ba-432">Aucun attribut n’est applicable à l’élément **Key** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-433">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-433">Example</span></span>

<span data-ttu-id="a19ba-434">L’exemple suivant montre un élément **EntityType** avec une clé qui référence une propriété :</span><span class="sxs-lookup"><span data-stu-id="a19ba-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="a19ba-435">OnDelete, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="a19ba-436">L’élément **OnDelete** en Store Schema Definition Language (SSDL) reflète le comportement de la base de données lorsqu’une ligne qui participe à une contrainte de clé étrangère est supprimée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="a19ba-437">Si l’action est définie sur **cascade**, les lignes qui font référence à une ligne en cours de suppression seront également supprimées.</span><span class="sxs-lookup"><span data-stu-id="a19ba-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="a19ba-438">Si l’action est définie sur **aucun**, les lignes qui font référence à une ligne en cours de suppression ne sont pas supprimées.</span><span class="sxs-lookup"><span data-stu-id="a19ba-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="a19ba-439">Un élément **OnDelete** est un élément enfant d’un élément end.</span><span class="sxs-lookup"><span data-stu-id="a19ba-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="a19ba-440">Un élément **OnDelete** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-441">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-442">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-443">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-443">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-444">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="a19ba-445">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-445">Attribute Name</span></span> | <span data-ttu-id="a19ba-446">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-446">Is Required</span></span> | <span data-ttu-id="a19ba-447">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-448">**Action**</span><span class="sxs-lookup"><span data-stu-id="a19ba-448">**Action**</span></span>     | <span data-ttu-id="a19ba-449">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-449">Yes</span></span>         | <span data-ttu-id="a19ba-450">**Cascade** ou **None**.</span><span class="sxs-lookup"><span data-stu-id="a19ba-450">**Cascade** or **None**.</span></span> <span data-ttu-id="a19ba-451">(La valeur **Restricted** est valide mais a le même comportement qu' **aucun**.)</span><span class="sxs-lookup"><span data-stu-id="a19ba-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-452">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="a19ba-453">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-454">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-455">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-455">Example</span></span>

<span data-ttu-id="a19ba-456">L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère \*\*FK \_ \*\* .</span><span class="sxs-lookup"><span data-stu-id="a19ba-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="a19ba-457">L’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="a19ba-458">Élément Parameter (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="a19ba-459">L’élément **Parameter** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui spécifie les paramètres d’une procédure stockée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="a19ba-460">L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-461">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-462">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-463">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-463">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-464">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="a19ba-465">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-465">Attribute Name</span></span> | <span data-ttu-id="a19ba-466">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-466">Is Required</span></span> | <span data-ttu-id="a19ba-467">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-468">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-468">**Name**</span></span>       | <span data-ttu-id="a19ba-469">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-469">Yes</span></span>         | <span data-ttu-id="a19ba-470">Le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="a19ba-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="a19ba-471">**Type**</span><span class="sxs-lookup"><span data-stu-id="a19ba-471">**Type**</span></span>       | <span data-ttu-id="a19ba-472">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-472">Yes</span></span>         | <span data-ttu-id="a19ba-473">Le type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="a19ba-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="a19ba-474">**Mode**</span><span class="sxs-lookup"><span data-stu-id="a19ba-474">**Mode**</span></span>       | <span data-ttu-id="a19ba-475">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-475">No</span></span>          | <span data-ttu-id="a19ba-476">**In**, **out**ou **INOUT** selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie.</span><span class="sxs-lookup"><span data-stu-id="a19ba-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="a19ba-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="a19ba-477">**MaxLength**</span></span>  | <span data-ttu-id="a19ba-478">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-478">No</span></span>          | <span data-ttu-id="a19ba-479">Longueur maximale du paramètre.</span><span class="sxs-lookup"><span data-stu-id="a19ba-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="a19ba-480">**Précision**</span><span class="sxs-lookup"><span data-stu-id="a19ba-480">**Precision**</span></span>  | <span data-ttu-id="a19ba-481">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-481">No</span></span>          | <span data-ttu-id="a19ba-482">La précision du paramètre.</span><span class="sxs-lookup"><span data-stu-id="a19ba-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="a19ba-483">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-483">**Scale**</span></span>      | <span data-ttu-id="a19ba-484">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-484">No</span></span>          | <span data-ttu-id="a19ba-485">L’échelle du paramètre.</span><span class="sxs-lookup"><span data-stu-id="a19ba-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="a19ba-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="a19ba-486">**SRID**</span></span>       | <span data-ttu-id="a19ba-487">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-487">No</span></span>          | <span data-ttu-id="a19ba-488">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="a19ba-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="a19ba-489">Valide uniquement pour les paramètres des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="a19ba-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="a19ba-490">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="a19ba-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-491">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="a19ba-492">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-493">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-494">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-494">Example</span></span>

<span data-ttu-id="a19ba-495">L’exemple suivant montre un élément **Function** qui possède deux éléments **Parameter** qui spécifient des paramètres d’entrée :</span><span class="sxs-lookup"><span data-stu-id="a19ba-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="a19ba-496">Élément Principal (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="a19ba-497">L’élément **principal** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison principale d’une contrainte de clé étrangère (également appelée contrainte référentielle).</span><span class="sxs-lookup"><span data-stu-id="a19ba-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="a19ba-498">L’élément **principal** spécifie la ou les colonnes de clé primaire d’une table qui est référencée par une ou plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="a19ba-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="a19ba-499">Les éléments **PropertyRef** spécifient les colonnes qui sont référencées.</span><span class="sxs-lookup"><span data-stu-id="a19ba-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="a19ba-500">L’élément dépendant spécifie les colonnes qui référencent les colonnes de clés primaires spécifiées dans l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="a19ba-501">L’élément **principal** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="a19ba-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a19ba-502">PropertyRef (un ou plusieurs) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="a19ba-503">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-504">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-504">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-505">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="a19ba-506">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-506">Attribute Name</span></span> | <span data-ttu-id="a19ba-507">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-507">Is Required</span></span> | <span data-ttu-id="a19ba-508">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-509">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-509">**Role**</span></span>       | <span data-ttu-id="a19ba-510">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-510">Yes</span></span>         | <span data-ttu-id="a19ba-511">La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; Sinon, le nom de la table qui contient la colonne référencée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-512">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="a19ba-513">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-514">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-515">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-515">Example</span></span>

<span data-ttu-id="a19ba-516">L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère \*\*FK \_ \*\* .</span><span class="sxs-lookup"><span data-stu-id="a19ba-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="a19ba-517">L’élément **principal** spécifie la colonne **CustomerID** de la table **Customer** comme terminaison principale de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="a19ba-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="a19ba-518">Property, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-518">Property Element (SSDL)</span></span>

<span data-ttu-id="a19ba-519">L’élément de **propriété** en Store Schema Definition Language (SSDL) représente une colonne dans une table de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="a19ba-520">Les éléments de **propriété** sont des enfants d’éléments EntityType, qui représentent des lignes dans une table.</span><span class="sxs-lookup"><span data-stu-id="a19ba-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="a19ba-521">Chaque élément de **propriété** défini sur un élément **EntityType** représente une colonne.</span><span class="sxs-lookup"><span data-stu-id="a19ba-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="a19ba-522">Un élément de **propriété** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="a19ba-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-523">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-523">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-524">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="a19ba-525">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-525">Attribute Name</span></span>            | <span data-ttu-id="a19ba-526">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-526">Is Required</span></span> | <span data-ttu-id="a19ba-527">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-528">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-528">**Name**</span></span>                  | <span data-ttu-id="a19ba-529">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-529">Yes</span></span>         | <span data-ttu-id="a19ba-530">Nom de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="a19ba-531">**Type**</span><span class="sxs-lookup"><span data-stu-id="a19ba-531">**Type**</span></span>                  | <span data-ttu-id="a19ba-532">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-532">Yes</span></span>         | <span data-ttu-id="a19ba-533">Type de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="a19ba-534">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="a19ba-534">**Nullable**</span></span>              | <span data-ttu-id="a19ba-535">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-535">No</span></span>          | <span data-ttu-id="a19ba-536">**True** (valeur par défaut) ou **false** selon que la colonne correspondante peut avoir une valeur null ou non.</span><span class="sxs-lookup"><span data-stu-id="a19ba-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="a19ba-537">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="a19ba-537">**DefaultValue**</span></span>          | <span data-ttu-id="a19ba-538">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-538">No</span></span>          | <span data-ttu-id="a19ba-539">Valeur par défaut de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="a19ba-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="a19ba-540">**MaxLength**</span></span>             | <span data-ttu-id="a19ba-541">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-541">No</span></span>          | <span data-ttu-id="a19ba-542">Longueur maximale de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="a19ba-543">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="a19ba-543">**FixedLength**</span></span>           | <span data-ttu-id="a19ba-544">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-544">No</span></span>          | <span data-ttu-id="a19ba-545">**True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="a19ba-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="a19ba-546">**Précision**</span><span class="sxs-lookup"><span data-stu-id="a19ba-546">**Precision**</span></span>             | <span data-ttu-id="a19ba-547">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-547">No</span></span>          | <span data-ttu-id="a19ba-548">Précision de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="a19ba-549">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-549">**Scale**</span></span>                 | <span data-ttu-id="a19ba-550">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-550">No</span></span>          | <span data-ttu-id="a19ba-551">Échelle de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="a19ba-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="a19ba-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="a19ba-552">**Unicode**</span></span>               | <span data-ttu-id="a19ba-553">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-553">No</span></span>          | <span data-ttu-id="a19ba-554">**True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="a19ba-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="a19ba-555">**Classement**</span><span class="sxs-lookup"><span data-stu-id="a19ba-555">**Collation**</span></span>             | <span data-ttu-id="a19ba-556">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-556">No</span></span>          | <span data-ttu-id="a19ba-557">Chaîne qui spécifie l’ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="a19ba-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="a19ba-558">**SRID**</span></span>                  | <span data-ttu-id="a19ba-559">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-559">No</span></span>          | <span data-ttu-id="a19ba-560">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="a19ba-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="a19ba-561">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="a19ba-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="a19ba-562">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="a19ba-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="a19ba-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="a19ba-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="a19ba-564">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-564">No</span></span>          | <span data-ttu-id="a19ba-565">**None**, **Identity** (si la valeur de colonne correspondante est une identité générée dans la base de données) ou **calculée** (si la valeur de colonne correspondante est calculée dans la base de données).</span><span class="sxs-lookup"><span data-stu-id="a19ba-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="a19ba-566">Non valide pour les propriétés RowType.</span><span class="sxs-lookup"><span data-stu-id="a19ba-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-567">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="a19ba-568">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-569">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-570">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-570">Example</span></span>

<span data-ttu-id="a19ba-571">L’exemple suivant montre un élément **EntityType** avec deux éléments de **propriété** enfants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="a19ba-572">Élément PropertyRef (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="a19ba-573">L’élément **PropertyRef** en Store Schema Definition Language (SSDL) fait référence à une propriété définie sur un élément EntityType pour indiquer que la propriété effectuera l’un des rôles suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="a19ba-574">Faire partie de la clé primaire de la table représentée par l' **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="a19ba-575">Un ou plusieurs éléments **PropertyRef** peuvent être utilisés pour définir une clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="a19ba-576">Pour plus d'informations, consultez Élément Key.</span><span class="sxs-lookup"><span data-stu-id="a19ba-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="a19ba-577">Faire partie de la terminaison dépendante ou principale d'une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="a19ba-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="a19ba-578">Pour plus d'informations, consultez Élément ReferentialConstraint.</span><span class="sxs-lookup"><span data-stu-id="a19ba-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="a19ba-579">L’élément **PropertyRef** ne peut avoir que les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="a19ba-580">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-581">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="a19ba-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-582">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-582">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-583">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="a19ba-584">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-584">Attribute Name</span></span> | <span data-ttu-id="a19ba-585">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-585">Is Required</span></span> | <span data-ttu-id="a19ba-586">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="a19ba-587">**Nom**</span><span class="sxs-lookup"><span data-stu-id="a19ba-587">**Name**</span></span>       | <span data-ttu-id="a19ba-588">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-588">Yes</span></span>         | <span data-ttu-id="a19ba-589">Nom de la propriété référencée.</span><span class="sxs-lookup"><span data-stu-id="a19ba-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="a19ba-590">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="a19ba-591">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="a19ba-592">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-593">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-593">Example</span></span>

<span data-ttu-id="a19ba-594">L’exemple suivant montre un élément **PropertyRef** utilisé pour définir une clé primaire en référençant une propriété définie sur un élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="a19ba-595">ReferentialConstraint, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="a19ba-596">L’élément **ReferentialConstraint** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère (également appelée contrainte d’intégrité référentielle) dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a19ba-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="a19ba-597">Les terminaisons principale et dépendante de la contrainte sont spécifiées respectivement par les éléments enfants Principal et Dependent.</span><span class="sxs-lookup"><span data-stu-id="a19ba-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="a19ba-598">Les colonnes qui participent aux terminaisons principale et dépendante sont référencées avec les éléments PropertyRef.</span><span class="sxs-lookup"><span data-stu-id="a19ba-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="a19ba-599">L’élément **ReferentialConstraint** est un élément enfant facultatif de l’élément Association.</span><span class="sxs-lookup"><span data-stu-id="a19ba-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="a19ba-600">Si un élément **ReferentialConstraint** n’est pas utilisé pour mapper la contrainte de clé étrangère spécifiée dans l’élément **Association** , un élément AssociationSetMapping doit être utilisé pour ce faire.</span><span class="sxs-lookup"><span data-stu-id="a19ba-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="a19ba-601">L’élément **ReferentialConstraint** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="a19ba-602">Documentation (zéro ou un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="a19ba-603">Principal (exactement un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="a19ba-604">Dependent (exactement un élément) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="a19ba-605">éléments d'annotation (zéro, un ou plusieurs).</span><span class="sxs-lookup"><span data-stu-id="a19ba-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-606">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-606">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-607">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="a19ba-608">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-609">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-610">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-610">Example</span></span>

<span data-ttu-id="a19ba-611">L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère \*\*FK \_ \*\* de la clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="a19ba-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="a19ba-612">ReturnType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="a19ba-613">L’élément **ReturnType** en Store Schema Definition Language (SSDL) spécifie le type de retour pour une fonction définie dans un élément **Function** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="a19ba-614">Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="a19ba-615">Le type de retour d’une fonction est spécifié à l’aide de l’attribut **type** ou de l’élément **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="a19ba-616">L’élément **ReturnType** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="a19ba-617">CollectionType (un)</span><span class="sxs-lookup"><span data-stu-id="a19ba-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="a19ba-618">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="a19ba-619">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="a19ba-620">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-621">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-621">Example</span></span>

<span data-ttu-id="a19ba-622">L’exemple suivant utilise une **fonction** qui retourne une collection de lignes.</span><span class="sxs-lookup"><span data-stu-id="a19ba-622">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="a19ba-623">RowType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="a19ba-624">Un élément **RowType** en Store Schema Definition Language (SSDL) définit une structure sans nom comme type de retour pour une fonction définie dans le magasin.</span><span class="sxs-lookup"><span data-stu-id="a19ba-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="a19ba-625">Un élément **RowType** est l’élément enfant de l’élément **CollectionType** :</span><span class="sxs-lookup"><span data-stu-id="a19ba-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="a19ba-626">Un élément **RowType** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="a19ba-627">Property (un ou plusieurs) ;</span><span class="sxs-lookup"><span data-stu-id="a19ba-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="a19ba-628">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-628">Example</span></span>

<span data-ttu-id="a19ba-629">L’exemple suivant montre une fonction de magasin qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="a19ba-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="a19ba-630">Schema, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="a19ba-631">L’élément **Schema** en Store Schema Definition Language (SSDL) est l’élément racine d’une définition de modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="a19ba-632">Il contient des définitions pour les objets, les fonctions et les conteneurs qui composent un modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="a19ba-633">L’élément **Schema** peut contenir zéro, un ou plusieurs des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="a19ba-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="a19ba-634">Association</span><span class="sxs-lookup"><span data-stu-id="a19ba-634">Association</span></span>
-   <span data-ttu-id="a19ba-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="a19ba-635">EntityType</span></span>
-   <span data-ttu-id="a19ba-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="a19ba-636">EntityContainer</span></span>
-   <span data-ttu-id="a19ba-637">Fonction</span><span class="sxs-lookup"><span data-stu-id="a19ba-637">Function</span></span>

<span data-ttu-id="a19ba-638">L’élément **Schema** utilise l’attribut **namespace** pour définir l’espace de noms du type d’entité et des objets Association dans un modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="a19ba-639">Dans un espace de noms, deux objets ne peuvent pas avoir le même nom.</span><span class="sxs-lookup"><span data-stu-id="a19ba-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="a19ba-640">Un espace de noms de modèle de stockage est différent de l’espace de noms XML de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="a19ba-641">Un espace de noms de modèle de stockage (tel que défini par l’attribut d' **espace de noms** ) est un conteneur logique pour les types d’entités et les types d’association.</span><span class="sxs-lookup"><span data-stu-id="a19ba-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="a19ba-642">L’espace de noms XML (indiqué par l’attribut **xmlns** ) d’un élément de **schéma** est l’espace de noms par défaut pour les éléments enfants et les attributs de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="a19ba-643">Les espaces de noms XML de la forme https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (où aaaa et mm représentent respectivement une année et un mois) sont réservés au langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="a19ba-644">Des éléments et attributs personnalisés ne peuvent pas être dans des espaces de noms de cette forme.</span><span class="sxs-lookup"><span data-stu-id="a19ba-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a19ba-645">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="a19ba-645">Applicable Attributes</span></span>

<span data-ttu-id="a19ba-646">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Schema** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="a19ba-647">Nom de l'attribut</span><span class="sxs-lookup"><span data-stu-id="a19ba-647">Attribute Name</span></span>            | <span data-ttu-id="a19ba-648">Est obligatoire</span><span class="sxs-lookup"><span data-stu-id="a19ba-648">Is Required</span></span> | <span data-ttu-id="a19ba-649">Valeur</span><span class="sxs-lookup"><span data-stu-id="a19ba-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-650">**Espace de noms**</span><span class="sxs-lookup"><span data-stu-id="a19ba-650">**Namespace**</span></span>             | <span data-ttu-id="a19ba-651">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-651">Yes</span></span>         | <span data-ttu-id="a19ba-652">Espace de noms du modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-652">The namespace of the storage model.</span></span> <span data-ttu-id="a19ba-653">La valeur de l’attribut d' **espace de noms** est utilisée pour former le nom qualifié complet d’un type.</span><span class="sxs-lookup"><span data-stu-id="a19ba-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="a19ba-654">Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store, le nom qualifié complet de l' **EntityType** est ExampleModel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="a19ba-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="a19ba-655">Les chaînes suivantes ne peuvent pas être utilisées comme valeur pour l’attribut d' **espace de noms** : **System**, **transient**ou **EDM**.</span><span class="sxs-lookup"><span data-stu-id="a19ba-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="a19ba-656">La valeur de l’attribut d' **espace de noms** ne peut pas être la même que la valeur de l’attribut d' **espace de noms** dans l’élément de schéma CSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="a19ba-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="a19ba-657">**Alias**</span></span>                 | <span data-ttu-id="a19ba-658">Non</span><span class="sxs-lookup"><span data-stu-id="a19ba-658">No</span></span>          | <span data-ttu-id="a19ba-659">Identificateur utilisé à la place du nom de l'espace de noms.</span><span class="sxs-lookup"><span data-stu-id="a19ba-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="a19ba-660">Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store et que la valeur de l’attribut **alias** est *StorageModel*, vous pouvez utiliser StorageModel. Customer comme nom qualifié complet de l' **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="a19ba-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="a19ba-661">**Fournisseur**</span><span class="sxs-lookup"><span data-stu-id="a19ba-661">**Provider**</span></span>              | <span data-ttu-id="a19ba-662">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-662">Yes</span></span>         | <span data-ttu-id="a19ba-663">Fournisseur de données.</span><span class="sxs-lookup"><span data-stu-id="a19ba-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="a19ba-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="a19ba-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="a19ba-665">Oui</span><span class="sxs-lookup"><span data-stu-id="a19ba-665">Yes</span></span>         | <span data-ttu-id="a19ba-666">Jeton qui indique au fournisseur quel manifeste de fournisseur retourner.</span><span class="sxs-lookup"><span data-stu-id="a19ba-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="a19ba-667">Aucun format n'est défini pour le jeton.</span><span class="sxs-lookup"><span data-stu-id="a19ba-667">No format for the token is defined.</span></span> <span data-ttu-id="a19ba-668">Les valeurs du jeton sont définies par le fournisseur.</span><span class="sxs-lookup"><span data-stu-id="a19ba-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="a19ba-669">Pour plus d’informations sur les jetons de manifeste du fournisseur SQL Server, consultez SqlClient pour Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a19ba-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="a19ba-670">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-670">Example</span></span>

<span data-ttu-id="a19ba-671">L’exemple suivant illustre un élément de **schéma** qui contient un élément **EntityContainer** , deux éléments **EntityType** et un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="a19ba-672">Attributs d'annotation</span><span class="sxs-lookup"><span data-stu-id="a19ba-672">Annotation Attributes</span></span>

<span data-ttu-id="a19ba-673">Les attributs d'annotation en SSDL (Store Schema Definition Language) sont des attributs XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant les éléments dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="a19ba-674">En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux attributs d'annotation :</span><span class="sxs-lookup"><span data-stu-id="a19ba-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="a19ba-675">Les attributs d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="a19ba-676">Les noms qualifiés complets de deux attributs d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="a19ba-677">Plusieurs attributs d'annotation peuvent être appliqués à un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="a19ba-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="a19ba-678">Vous pouvez accéder aux métadonnées contenues dans les éléments d’annotation au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="a19ba-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-679">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-679">Example</span></span>

<span data-ttu-id="a19ba-680">L’exemple suivant montre un élément EntityType qui a un attribut d’annotation appliqué à la propriété **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="a19ba-681">L’exemple montre également un élément d’annotation ajouté à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-681">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="a19ba-682">Éléments d'annotation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="a19ba-683">Les éléments d'annotation en SSDL (Store Schema Definition Language) sont des éléments XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="a19ba-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="a19ba-684">En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux éléments d'annotation :</span><span class="sxs-lookup"><span data-stu-id="a19ba-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="a19ba-685">Les éléments d'annotation ne doivent pas figurer dans un espace de noms XML réservé au langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="a19ba-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="a19ba-686">Les noms qualifiés complets de deux éléments d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="a19ba-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="a19ba-687">Les éléments d'annotation doivent apparaître après tous les autres éléments enfants d'un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="a19ba-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="a19ba-688">Plusieurs éléments d'annotation peuvent être des enfants d'un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="a19ba-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="a19ba-689">À partir de la .NET Framework version 4, les métadonnées contenues dans les éléments d’annotation sont accessibles au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="a19ba-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="a19ba-690">Exemple</span><span class="sxs-lookup"><span data-stu-id="a19ba-690">Example</span></span>

<span data-ttu-id="a19ba-691">L’exemple suivant montre un élément EntityType qui a un élément annotation (**customelement**).</span><span class="sxs-lookup"><span data-stu-id="a19ba-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="a19ba-692">L’exemple montre également un attribut d’annotation appliqué à la propriété **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="a19ba-693">Facettes (SSDL)</span><span class="sxs-lookup"><span data-stu-id="a19ba-693">Facets (SSDL)</span></span>

<span data-ttu-id="a19ba-694">Les facettes en SSDL (Store Schema Definition Language) représentent des contraintes sur les types de colonne spécifiés dans les éléments Property.</span><span class="sxs-lookup"><span data-stu-id="a19ba-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="a19ba-695">Les facettes sont implémentées en tant qu’attributs XML sur les éléments de **propriété** .</span><span class="sxs-lookup"><span data-stu-id="a19ba-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="a19ba-696">Le tableau ci-dessous décrit les facettes prises en charge dans le langage SSDL :</span><span class="sxs-lookup"><span data-stu-id="a19ba-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="a19ba-697">Facette</span><span class="sxs-lookup"><span data-stu-id="a19ba-697">Facet</span></span>           | <span data-ttu-id="a19ba-698">Description</span><span class="sxs-lookup"><span data-stu-id="a19ba-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a19ba-699">**Classement**</span><span class="sxs-lookup"><span data-stu-id="a19ba-699">**Collation**</span></span>   | <span data-ttu-id="a19ba-700">Spécifie la table de classement ou ordre de tri à utiliser lors de l'exécution d'opérations de comparaison et de tri sur des valeurs de la propriété.</span><span class="sxs-lookup"><span data-stu-id="a19ba-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="a19ba-701">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="a19ba-701">**FixedLength**</span></span> | <span data-ttu-id="a19ba-702">Spécifie si la longueur de la valeur de colonne peut varier.</span><span class="sxs-lookup"><span data-stu-id="a19ba-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="a19ba-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="a19ba-703">**MaxLength**</span></span>   | <span data-ttu-id="a19ba-704">Spécifie la longueur maximale de la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="a19ba-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="a19ba-705">**Précision**</span><span class="sxs-lookup"><span data-stu-id="a19ba-705">**Precision**</span></span>   | <span data-ttu-id="a19ba-706">Pour les propriétés de type **Decimal**, spécifie le nombre de chiffres qu’une valeur de propriété peut avoir.</span><span class="sxs-lookup"><span data-stu-id="a19ba-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="a19ba-707">Pour les propriétés de type **Time**, **DateTime**et **DateTimeOffset**, spécifie le nombre de chiffres pour la partie fractionnaire des secondes de la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="a19ba-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="a19ba-708">**Mettre à l'échelle**</span><span class="sxs-lookup"><span data-stu-id="a19ba-708">**Scale**</span></span>       | <span data-ttu-id="a19ba-709">Spécifie le nombre de chiffres à droite de la virgule décimale pour la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="a19ba-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="a19ba-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="a19ba-710">**Unicode**</span></span>     | <span data-ttu-id="a19ba-711">Indique si la valeur de colonne est stockée au format Unicode.</span><span class="sxs-lookup"><span data-stu-id="a19ba-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
