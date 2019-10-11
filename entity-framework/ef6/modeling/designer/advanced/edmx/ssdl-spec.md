---
title: Spécification SSDL-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182549"
---
# <a name="ssdl-specification"></a><span data-ttu-id="06f82-102">Spécification SSDL</span><span class="sxs-lookup"><span data-stu-id="06f82-102">SSDL Specification</span></span>
<span data-ttu-id="06f82-103">SSDL (Store Schema Definition Language) est un langage basé sur XML qui décrit le modèle de stockage d'une application Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="06f82-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="06f82-104">Dans une application Entity Framework, les métadonnées du modèle de stockage sont chargées à partir d’un fichier. SSDL (écrit en SSDL) dans une instance de System. Data. Metadata. Edm. StoreItemCollection et sont accessibles à l’aide des méthodes de la Classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="06f82-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="06f82-105">Entity Framework utilise les métadonnées du modèle de stockage pour traduire les requêtes sur le modèle conceptuel en commandes spécifiques au stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="06f82-106">Le Entity Framework Designer (concepteur EF) stocke les informations de modèle de stockage dans un fichier. edmx au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="06f82-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="06f82-107">Au moment de la génération, le Entity Designer utilise les informations d’un fichier. edmx pour créer le fichier. ssdl requis par Entity Framework au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="06f82-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="06f82-108">Les versions de SSDL sont différenciées par les espaces de noms XML.</span><span class="sxs-lookup"><span data-stu-id="06f82-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="06f82-109">Version SSDL</span><span class="sxs-lookup"><span data-stu-id="06f82-109">SSDL Version</span></span> | <span data-ttu-id="06f82-110">Espace de noms XML</span><span class="sxs-lookup"><span data-stu-id="06f82-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="06f82-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="06f82-111">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="06f82-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="06f82-112">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="06f82-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="06f82-113">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="06f82-114">Association, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-114">Association Element (SSDL)</span></span>

<span data-ttu-id="06f82-115">Un élément **Association** en Store Schema Definition Language (SSDL) spécifie des colonnes de table qui participent à une contrainte de clé étrangère dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="06f82-116">Deux éléments end enfants requis spécifient des tables aux terminaisons de l’Association et la multiplicité à chaque extrémité.</span><span class="sxs-lookup"><span data-stu-id="06f82-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="06f82-117">Un élément ReferentialConstraint facultatif spécifie les terminaisons principale et dépendante de l’Association ainsi que les colonnes participantes.</span><span class="sxs-lookup"><span data-stu-id="06f82-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="06f82-118">Si aucun élément **ReferentialConstraint** n’est présent, un élément AssociationSetMapping doit être utilisé pour spécifier les mappages de colonnes pour l’Association.</span><span class="sxs-lookup"><span data-stu-id="06f82-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="06f82-119">L’élément **Association** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-120">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-121">End (exactement deux)</span><span class="sxs-lookup"><span data-stu-id="06f82-121">End (exactly two)</span></span>
-   <span data-ttu-id="06f82-122">ReferentialConstraint (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="06f82-123">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-124">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-124">Applicable Attributes</span></span>

<span data-ttu-id="06f82-125">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="06f82-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="06f82-126">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-126">Attribute Name</span></span> | <span data-ttu-id="06f82-127">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-127">Is Required</span></span> | <span data-ttu-id="06f82-128">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-129">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-129">**Name**</span></span>       | <span data-ttu-id="06f82-130">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-130">Yes</span></span>         | <span data-ttu-id="06f82-131">Nom de la contrainte de clé étrangère correspondante dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-132">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="06f82-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="06f82-133">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-134">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-135">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-135">Example</span></span>

<span data-ttu-id="06f82-136">L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK @ no__t-3CustomerOrders** :</span><span class="sxs-lookup"><span data-stu-id="06f82-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="06f82-137">AssociationSet, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="06f82-138">L’élément **AssociationSet** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère entre deux tables dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="06f82-139">Les colonnes de table qui participent à la contrainte de clé étrangère sont spécifiées dans un élément Association.</span><span class="sxs-lookup"><span data-stu-id="06f82-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="06f82-140">L’élément **Association** qui correspond à un élément **AssociationSet** donné est spécifié dans l’attribut **Association** de l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="06f82-141">Les ensembles d’associations SSDL sont mappés aux ensembles d’associations CSDL par un élément AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="06f82-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="06f82-142">Toutefois, si l’Association CSDL pour un ensemble d’associations CSDL donné est définie à l’aide d’un élément ReferentialConstraint, aucun élément **AssociationSetMapping** correspondant n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="06f82-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="06f82-143">Dans ce cas, si un élément **AssociationSetMapping** est présent, les mappages qu’il définit seront remplacés par l’élément **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="06f82-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="06f82-144">L’élément **AssociationSet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-145">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-146">End (zéro ou deux)</span><span class="sxs-lookup"><span data-stu-id="06f82-146">End (zero or two)</span></span>
-   <span data-ttu-id="06f82-147">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-148">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-148">Applicable Attributes</span></span>

<span data-ttu-id="06f82-149">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="06f82-150">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-150">Attribute Name</span></span>  | <span data-ttu-id="06f82-151">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-151">Is Required</span></span> | <span data-ttu-id="06f82-152">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-153">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-153">**Name**</span></span>        | <span data-ttu-id="06f82-154">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-154">Yes</span></span>         | <span data-ttu-id="06f82-155">Nom de la contrainte de clé étrangère que l'ensemble d'associations représente.</span><span class="sxs-lookup"><span data-stu-id="06f82-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="06f82-156">**Association**</span><span class="sxs-lookup"><span data-stu-id="06f82-156">**Association**</span></span> | <span data-ttu-id="06f82-157">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-157">Yes</span></span>         | <span data-ttu-id="06f82-158">Nom de l'association qui définit les colonnes qui participent à la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-159">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="06f82-160">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-161">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-162">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-162">Example</span></span>

<span data-ttu-id="06f82-163">L’exemple suivant montre un élément **AssociationSet** qui représente la contrainte de clé étrangère `FK_CustomerOrders` dans la base de données sous-jacente :</span><span class="sxs-lookup"><span data-stu-id="06f82-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="06f82-164">Élément CollectionType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="06f82-165">L’élément **CollectionType** en Store Schema Definition Language (SSDL) spécifie que le type de retour d’une fonction est une collection.</span><span class="sxs-lookup"><span data-stu-id="06f82-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="06f82-166">L’élément **CollectionType** est un enfant de l’élément ReturnType.</span><span class="sxs-lookup"><span data-stu-id="06f82-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="06f82-167">Le type de collection est spécifié à l’aide de l’élément enfant RowType :</span><span class="sxs-lookup"><span data-stu-id="06f82-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="06f82-168">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="06f82-169">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-170">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-171">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-171">Example</span></span>

<span data-ttu-id="06f82-172">L’exemple suivant montre une fonction qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes.</span><span class="sxs-lookup"><span data-stu-id="06f82-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="06f82-173">CommandText, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="06f82-174">L’élément **CommandText** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui vous permet de définir une instruction SQL qui est exécutée au niveau de la base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="06f82-175">L’élément **CommandText** vous permet d’ajouter des fonctionnalités qui sont similaires à une procédure stockée dans la base de données, mais vous définissez l’élément **CommandText** dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="06f82-176">L’élément **CommandText** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="06f82-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="06f82-177">Le corps de l’élément **CommandText** doit être une instruction SQL valide pour la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="06f82-178">Aucun attribut n’est applicable à l’élément **CommandText** .</span><span class="sxs-lookup"><span data-stu-id="06f82-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-179">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-179">Example</span></span>

<span data-ttu-id="06f82-180">L’exemple suivant montre un élément **Function** avec un élément **CommandText** enfant.</span><span class="sxs-lookup"><span data-stu-id="06f82-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="06f82-181">Exposez la fonction **UpdateProductInOrder** en tant que méthode sur ObjectContext en l’important dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="06f82-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="06f82-182">DefiningQuery, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="06f82-183">L’élément **DefiningQuery** en Store Schema Definition Language (SSDL) vous permet d’exécuter une instruction SQL directement dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="06f82-184">L’élément **DefiningQuery** est couramment utilisé comme une vue de base de données, mais la vue est définie dans le modèle de stockage au lieu de la base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="06f82-185">La vue définie dans un élément **DefiningQuery** peut être mappée à un type d’entité dans le modèle conceptuel via un élément EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="06f82-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="06f82-186">Ces mappages sont en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="06f82-186">These mappings are read-only.</span></span>  

<span data-ttu-id="06f82-187">La syntaxe SSDL suivante illustre la déclaration d’un **EntitySet** suivi de l’élément **DefiningQuery** qui contient une requête utilisée pour récupérer la vue.</span><span class="sxs-lookup"><span data-stu-id="06f82-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="06f82-188">Vous pouvez utiliser des procédures stockées dans le Entity Framework pour activer des scénarios de lecture-écriture sur des vues.</span><span class="sxs-lookup"><span data-stu-id="06f82-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="06f82-189"> Vous pouvez utiliser une vue de source de données ou une vue de Entity SQL comme table de base pour récupérer des données et pour le traitement des modifications par des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="06f82-189"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="06f82-190">Vous pouvez utiliser l’élément **DefiningQuery** pour cibler Microsoft SQL Server Compact 3,5.</span><span class="sxs-lookup"><span data-stu-id="06f82-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="06f82-191">Bien que SQL Server Compact 3,5 ne prenne pas en charge les procédures stockées, vous pouvez implémenter des fonctionnalités similaires avec l’élément **DefiningQuery** .</span><span class="sxs-lookup"><span data-stu-id="06f82-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="06f82-192">Cet élément peut s'avérer également utile pour créer des procédures stockées afin de surmonter une incompatibilité entre les types de données utilisés dans le langage de programmation et ceux de la source de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="06f82-193">Vous pouvez écrire un **DefiningQuery** qui accepte un certain ensemble de paramètres, puis appelle une procédure stockée avec un jeu de paramètres différent, par exemple, une procédure stockée qui supprime des données.</span><span class="sxs-lookup"><span data-stu-id="06f82-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="06f82-194">Élément Dependent (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="06f82-195">L’élément **dépendant** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison dépendante d’une contrainte de clé étrangère (également appelée contrainte référentielle).</span><span class="sxs-lookup"><span data-stu-id="06f82-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="06f82-196">L’élément **dépendant** spécifie la ou les colonnes d’une table qui font référence à une ou plusieurs colonnes de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="06f82-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="06f82-197">Les éléments **PropertyRef** spécifient les colonnes qui sont référencées.</span><span class="sxs-lookup"><span data-stu-id="06f82-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="06f82-198">L’élément principal spécifie les colonnes de clés primaires référencées par les colonnes spécifiées dans l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="06f82-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="06f82-199">L’élément **dépendant** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-200">PropertyRef (un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="06f82-201">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-202">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-202">Applicable Attributes</span></span>

<span data-ttu-id="06f82-203">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="06f82-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="06f82-204">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-204">Attribute Name</span></span> | <span data-ttu-id="06f82-205">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-205">Is Required</span></span> | <span data-ttu-id="06f82-206">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-207">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="06f82-207">**Role**</span></span>       | <span data-ttu-id="06f82-208">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-208">Yes</span></span>         | <span data-ttu-id="06f82-209">La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; dans le cas contraire, il s’agit du nom de la table qui contient la colonne de référence.</span><span class="sxs-lookup"><span data-stu-id="06f82-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-210">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **dépendant** .</span><span class="sxs-lookup"><span data-stu-id="06f82-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="06f82-211">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-212">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-213">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-213">Example</span></span>

<span data-ttu-id="06f82-214">L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK @ no__t-2CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="06f82-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="06f82-215">L’élément **dépendant** spécifie la colonne **CustomerID** de la table **Order** comme terminaison dépendante de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="06f82-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="06f82-216">Documentation, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="06f82-217">L’élément **documentation** en Store Schema Definition Language (SSDL) peut être utilisé pour fournir des informations sur un objet défini dans un élément parent.</span><span class="sxs-lookup"><span data-stu-id="06f82-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="06f82-218">L’élément **documentation** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-219">**Résumé**: Brève description de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="06f82-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="06f82-220">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="06f82-220">(zero or one element)</span></span>
-   <span data-ttu-id="06f82-221">**LongDescription**: Description complète de l’élément parent.</span><span class="sxs-lookup"><span data-stu-id="06f82-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="06f82-222">(zéro ou un élément).</span><span class="sxs-lookup"><span data-stu-id="06f82-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-223">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-223">Applicable Attributes</span></span>

<span data-ttu-id="06f82-224">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **documentation** .</span><span class="sxs-lookup"><span data-stu-id="06f82-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="06f82-225">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-226">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-227">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-227">Example</span></span>

<span data-ttu-id="06f82-228">L’exemple suivant montre l’élément de **documentation** en tant qu’élément enfant d’un élément EntityType.</span><span class="sxs-lookup"><span data-stu-id="06f82-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="06f82-229">End, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-229">End Element (SSDL)</span></span>

<span data-ttu-id="06f82-230">L’élément **end** en Store Schema Definition Language (SSDL) spécifie la table et le nombre de lignes à une terminaison d’une contrainte FOREIGN KEY dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="06f82-231">L’élément **end** peut être un enfant de l’élément Association ou de l’élément AssociationSet.</span><span class="sxs-lookup"><span data-stu-id="06f82-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="06f82-232">Dans chaque cas, les éléments enfants et les attributs applicables possibles sont différents.</span><span class="sxs-lookup"><span data-stu-id="06f82-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="06f82-233">Élément End comme enfant de l'élément Association</span><span class="sxs-lookup"><span data-stu-id="06f82-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="06f82-234">Un élément **end** (en tant qu’enfant de l’élément **Association** ) spécifie la table et le nombre de lignes à la fin d’une contrainte de clé étrangère, respectivement avec les attributs **type** et **Multiplicity** .</span><span class="sxs-lookup"><span data-stu-id="06f82-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="06f82-235">Les terminaisons d'une contrainte de clé étrangère sont définies dans le cadre d'un ensemble d'associations SSDL ; un ensemble d'associations SSDL doit avoir exactement deux terminaisons.</span><span class="sxs-lookup"><span data-stu-id="06f82-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="06f82-236">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-237">Documentation (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="06f82-238">OnDelete (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="06f82-239">Éléments d’annotation (zéro, un ou plusieurs éléments)</span><span class="sxs-lookup"><span data-stu-id="06f82-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="06f82-240">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-240">Applicable Attributes</span></span>

<span data-ttu-id="06f82-241">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **final** lorsqu’il est l’enfant d’un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="06f82-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="06f82-242">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-242">Attribute Name</span></span>   | <span data-ttu-id="06f82-243">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-243">Is Required</span></span> | <span data-ttu-id="06f82-244">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-245">**Type**</span><span class="sxs-lookup"><span data-stu-id="06f82-245">**Type**</span></span>         | <span data-ttu-id="06f82-246">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-246">Yes</span></span>         | <span data-ttu-id="06f82-247">Nom qualifié complet du jeu d’entités SSDL qui est à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="06f82-248">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="06f82-248">**Role**</span></span>         | <span data-ttu-id="06f82-249">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-249">No</span></span>          | <span data-ttu-id="06f82-250">Valeur de l’attribut **role** dans l’élément principal ou dépendant de l’élément ReferentialConstraint correspondant (s’il est utilisé).</span><span class="sxs-lookup"><span data-stu-id="06f82-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="06f82-251">**Multiplicité**</span><span class="sxs-lookup"><span data-stu-id="06f82-251">**Multiplicity**</span></span> | <span data-ttu-id="06f82-252">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-252">Yes</span></span>         | <span data-ttu-id="06f82-253">**1**, **0.. 1**ou **\*** en fonction du nombre de lignes qui peuvent être à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="06f82-254">**1** indique qu’une seule ligne existe à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="06f82-255">**0.. 1** indique qu’il existe zéro ou une ligne à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="06f82-256">**\*** indique que zéro, une ou plusieurs lignes existent à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-257">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="06f82-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="06f82-258">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-259">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="06f82-260">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-260">Example</span></span>

<span data-ttu-id="06f82-261">L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère **FK @ no__t-2CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="06f82-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="06f82-262">Les valeurs de **multiplicité** spécifiées sur chaque élément de **fin** indiquent que de nombreuses lignes de la table **Orders** peuvent être associées à une ligne de la table **Customers** , mais qu’une seule ligne de la table **Customers** peut être associée à une ligne dans la table **Orders** .</span><span class="sxs-lookup"><span data-stu-id="06f82-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="06f82-263">En outre, l’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.</span><span class="sxs-lookup"><span data-stu-id="06f82-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="06f82-264">Élément End comme enfant de l'élément AssociationSet</span><span class="sxs-lookup"><span data-stu-id="06f82-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="06f82-265">L’élément **end** (en tant qu’enfant de l’élément **AssociationSet** ) spécifie une table à une terminaison d’une contrainte de clé étrangère dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="06f82-266">Un élément **end** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-267">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-268">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="06f82-269">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-269">Applicable Attributes</span></span>

<span data-ttu-id="06f82-270">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **end** lorsqu’il est l’enfant d’un élément **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="06f82-271">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-271">Attribute Name</span></span> | <span data-ttu-id="06f82-272">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-272">Is Required</span></span> | <span data-ttu-id="06f82-273">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="06f82-274">**EntitySet**</span></span>  | <span data-ttu-id="06f82-275">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-275">Yes</span></span>         | <span data-ttu-id="06f82-276">Nom du jeu d’entités SSDL qui est à la fin de la contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="06f82-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="06f82-277">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="06f82-277">**Role**</span></span>       | <span data-ttu-id="06f82-278">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-278">No</span></span>          | <span data-ttu-id="06f82-279">Valeur de l’un des attributs de **rôle** spécifiés sur un élément de **fin** de l’élément Association correspondant.</span><span class="sxs-lookup"><span data-stu-id="06f82-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-280">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fin** .</span><span class="sxs-lookup"><span data-stu-id="06f82-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="06f82-281">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-282">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="06f82-283">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-283">Example</span></span>

<span data-ttu-id="06f82-284">L’exemple suivant montre un élément **EntityContainer** avec un élément **AssociationSet** avec deux éléments **end** :</span><span class="sxs-lookup"><span data-stu-id="06f82-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="06f82-285">EntityContainer, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="06f82-286">Un élément **EntityContainer** en Store Schema Definition Language (SSDL) décrit la structure de la source de données sous-jacente dans une application Entity Framework : Les jeux d’entités SSDL (définis dans les éléments EntitySet) représentent les tables d’une base de données, les types d’entités SSDL (définis dans les éléments EntityType) représentent les lignes d’une table, et les ensembles d’associations (définis dans les éléments AssociationSet) représentent des contraintes de clé étrangère dans un Database.</span><span class="sxs-lookup"><span data-stu-id="06f82-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="06f82-287">Un conteneur d’entités de modèle de stockage est mappé à un conteneur d’entités de modèle conceptuel par le biais de l’élément EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="06f82-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="06f82-288">Un élément **EntityContainer** peut avoir zéro ou un élément documentation.</span><span class="sxs-lookup"><span data-stu-id="06f82-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="06f82-289">Si un élément de **documentation** est présent, il doit précéder tous les autres éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="06f82-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="06f82-290">Un élément **EntityContainer** peut avoir zéro ou plusieurs des éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-291">EntitySet ;</span><span class="sxs-lookup"><span data-stu-id="06f82-291">EntitySet</span></span>
-   <span data-ttu-id="06f82-292">AssociationSet ;</span><span class="sxs-lookup"><span data-stu-id="06f82-292">AssociationSet</span></span>
-   <span data-ttu-id="06f82-293">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="06f82-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-294">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-294">Applicable Attributes</span></span>

<span data-ttu-id="06f82-295">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="06f82-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="06f82-296">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-296">Attribute Name</span></span> | <span data-ttu-id="06f82-297">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-297">Is Required</span></span> | <span data-ttu-id="06f82-298">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="06f82-299">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-299">**Name**</span></span>       | <span data-ttu-id="06f82-300">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-300">Yes</span></span>         | <span data-ttu-id="06f82-301">Nom du conteneur d'entités.</span><span class="sxs-lookup"><span data-stu-id="06f82-301">The name of the entity container.</span></span> <span data-ttu-id="06f82-302">Ce nom ne peut pas contenir de point (.).</span><span class="sxs-lookup"><span data-stu-id="06f82-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-303">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="06f82-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="06f82-304">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-305">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-306">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-306">Example</span></span>

<span data-ttu-id="06f82-307">L’exemple suivant montre un élément **EntityContainer** qui définit deux jeux d’entités et un ensemble d’associations.</span><span class="sxs-lookup"><span data-stu-id="06f82-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="06f82-308">Notez que les noms de type d'entité et de type d'association sont qualifiés par le nom de l'espace de noms du modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="06f82-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="06f82-309">EntitySet, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="06f82-310">Un élément **EntitySet** en Store Schema Definition Language (SSDL) représente une table ou une vue dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="06f82-311">Un élément EntityType en SSDL représente une ligne dans la table ou la vue.</span><span class="sxs-lookup"><span data-stu-id="06f82-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="06f82-312">L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="06f82-313">Le mappage entre un jeu d’entités CSDL et un jeu d’entités SSDL est spécifié dans un élément EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="06f82-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="06f82-314">L’élément **EntitySet** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-315">Documentation (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="06f82-316">DefiningQuery (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="06f82-317">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="06f82-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-318">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-318">Applicable Attributes</span></span>

<span data-ttu-id="06f82-319">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="06f82-320">Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** .</span><span class="sxs-lookup"><span data-stu-id="06f82-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="06f82-321">Ces attributs sont utilisés par l’Assistant Mise à jour du modèle lors de la mise à jour d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="06f82-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="06f82-322">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-322">Attribute Name</span></span> | <span data-ttu-id="06f82-323">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-323">Is Required</span></span> | <span data-ttu-id="06f82-324">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-325">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-325">**Name**</span></span>       | <span data-ttu-id="06f82-326">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-326">Yes</span></span>         | <span data-ttu-id="06f82-327">Nom du jeu d'entités.</span><span class="sxs-lookup"><span data-stu-id="06f82-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="06f82-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="06f82-328">**EntityType**</span></span> | <span data-ttu-id="06f82-329">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-329">Yes</span></span>         | <span data-ttu-id="06f82-330">Nom qualifié complet du type d'entité pour lequel le jeu d'entités contient des instances.</span><span class="sxs-lookup"><span data-stu-id="06f82-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="06f82-331">**Schéma**</span><span class="sxs-lookup"><span data-stu-id="06f82-331">**Schema**</span></span>     | <span data-ttu-id="06f82-332">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-332">No</span></span>          | <span data-ttu-id="06f82-333">Schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="06f82-334">**Table**</span><span class="sxs-lookup"><span data-stu-id="06f82-334">**Table**</span></span>      | <span data-ttu-id="06f82-335">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-335">No</span></span>          | <span data-ttu-id="06f82-336">Table de base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="06f82-337">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="06f82-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="06f82-338">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-339">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-340">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-340">Example</span></span>

<span data-ttu-id="06f82-341">L’exemple suivant montre un élément **EntityContainer** qui a deux éléments **EntitySet** et un élément **AssociationSet** :</span><span class="sxs-lookup"><span data-stu-id="06f82-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="06f82-342">EntityType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="06f82-343">Un élément **EntityType** en Store Schema Definition Language (SSDL) représente une ligne dans une table ou une vue de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="06f82-344">Un élément EntitySet en SSDL représente la table ou la vue dans laquelle les lignes se trouvent.</span><span class="sxs-lookup"><span data-stu-id="06f82-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="06f82-345">L’attribut **EntityType** d’un élément **EntitySet** spécifie le type d’entité SSDL particulier qui représente les lignes dans un jeu d’entités SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="06f82-346">Le mappage entre un type d’entité SSDL et un type d’entité CSDL est spécifié dans un élément EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="06f82-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="06f82-347">L’élément **EntityType** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-348">Documentation (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="06f82-349">Key (zéro ou un élément)</span><span class="sxs-lookup"><span data-stu-id="06f82-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="06f82-350">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="06f82-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-351">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-351">Applicable Attributes</span></span>

<span data-ttu-id="06f82-352">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="06f82-353">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-353">Attribute Name</span></span> | <span data-ttu-id="06f82-354">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-354">Is Required</span></span> | <span data-ttu-id="06f82-355">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-356">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-356">**Name**</span></span>       | <span data-ttu-id="06f82-357">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-357">Yes</span></span>         | <span data-ttu-id="06f82-358">Nom du type d'entité.</span><span class="sxs-lookup"><span data-stu-id="06f82-358">The name of the entity type.</span></span> <span data-ttu-id="06f82-359">Cette valeur est habituellement la même que le nom de la table dans laquelle le type d'entité représente une ligne.</span><span class="sxs-lookup"><span data-stu-id="06f82-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="06f82-360">Cette valeur ne peut pas contenir de point (.).</span><span class="sxs-lookup"><span data-stu-id="06f82-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-361">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="06f82-362">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-363">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-364">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-364">Example</span></span>

<span data-ttu-id="06f82-365">L’exemple suivant montre un élément **EntityType** avec deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="06f82-365">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="06f82-366">Function, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-366">Function Element (SSDL)</span></span>

<span data-ttu-id="06f82-367">L’élément **Function** de Store Schema Definition Language (SSDL) spécifie une procédure stockée qui existe dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="06f82-368">L’élément **Function** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-369">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-370">Paramètre (zéro, un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="06f82-371">CommandText (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="06f82-372">ReturnType (zéro, un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="06f82-373">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="06f82-374">Un type de retour pour une fonction doit être spécifié avec l’élément **ReturnType** ou l’attribut **ReturnType** (voir ci-dessous), mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="06f82-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="06f82-375">Les procédures stockées spécifiées dans le modèle de stockage peuvent être importées dans le modèle conceptuel d'une application.</span><span class="sxs-lookup"><span data-stu-id="06f82-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="06f82-376">Pour plus d’informations, consultez [interrogation avec des procédures stockées](~/ef6/modeling/designer/stored-procedures/query.md).</span><span class="sxs-lookup"><span data-stu-id="06f82-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="06f82-377">L’élément **Function** peut également être utilisé pour définir des fonctions personnalisées dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-378">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-378">Applicable Attributes</span></span>

<span data-ttu-id="06f82-379">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Function** .</span><span class="sxs-lookup"><span data-stu-id="06f82-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="06f82-380">Certains attributs (non répertoriés ici) peuvent être qualifiés avec l’alias du **magasin** .</span><span class="sxs-lookup"><span data-stu-id="06f82-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="06f82-381">Ces attributs sont utilisés par l’Assistant Mise à jour du modèle lors de la mise à jour d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="06f82-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="06f82-382">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-382">Attribute Name</span></span>             | <span data-ttu-id="06f82-383">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-383">Is Required</span></span> | <span data-ttu-id="06f82-384">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-385">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-385">**Name**</span></span>                   | <span data-ttu-id="06f82-386">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-386">Yes</span></span>         | <span data-ttu-id="06f82-387">Nom de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="06f82-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="06f82-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="06f82-388">**ReturnType**</span></span>             | <span data-ttu-id="06f82-389">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-389">No</span></span>          | <span data-ttu-id="06f82-390">Type de retour de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="06f82-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="06f82-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="06f82-391">**Aggregate**</span></span>              | <span data-ttu-id="06f82-392">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-392">No</span></span>          | <span data-ttu-id="06f82-393">**True** si la procédure stockée retourne une valeur d’agrégation ; Sinon, **false**.</span><span class="sxs-lookup"><span data-stu-id="06f82-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="06f82-394">**Intégrée**</span><span class="sxs-lookup"><span data-stu-id="06f82-394">**BuiltIn**</span></span>                | <span data-ttu-id="06f82-395">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-395">No</span></span>          | <span data-ttu-id="06f82-396">**True** si la fonction est une fonction intégrée<sup>1</sup> ; Sinon, **false**.</span><span class="sxs-lookup"><span data-stu-id="06f82-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="06f82-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="06f82-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="06f82-398">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-398">No</span></span>          | <span data-ttu-id="06f82-399">Nom de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="06f82-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="06f82-400">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="06f82-400">**NiladicFunction**</span></span>        | <span data-ttu-id="06f82-401">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-401">No</span></span>          | <span data-ttu-id="06f82-402">**True** si la fonction est une fonction niladic<sup>2</sup> ; **False** dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="06f82-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="06f82-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="06f82-403">**IsComposable**</span></span>           | <span data-ttu-id="06f82-404">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-404">No</span></span>          | <span data-ttu-id="06f82-405">**True** si la fonction est une fonction composable<sup>3</sup> ; **False** dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="06f82-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="06f82-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="06f82-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="06f82-407">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-407">No</span></span>          | <span data-ttu-id="06f82-408">Énumération qui définit la sémantique de type utilisée pour résoudre les surcharges de fonction.</span><span class="sxs-lookup"><span data-stu-id="06f82-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="06f82-409">L'énumération est définie dans le manifeste du fournisseur par définition de fonction.</span><span class="sxs-lookup"><span data-stu-id="06f82-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="06f82-410">La valeur par défaut est **AllowImplicitConversion**.</span><span class="sxs-lookup"><span data-stu-id="06f82-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="06f82-411">**Schéma**</span><span class="sxs-lookup"><span data-stu-id="06f82-411">**Schema**</span></span>                 | <span data-ttu-id="06f82-412">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-412">No</span></span>          | <span data-ttu-id="06f82-413">Nom du schéma dans lequel une procédure stockée est définie.</span><span class="sxs-lookup"><span data-stu-id="06f82-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="06f82-414"><sup>1</sup> une fonction intégrée est une fonction définie dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="06f82-415">Pour plus d’informations sur les fonctions définies dans le modèle de stockage, consultez CommandText, élément (SSDL).</span><span class="sxs-lookup"><span data-stu-id="06f82-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="06f82-416"><sup>2</sup> une fonction niladic est une fonction qui n’accepte aucun paramètre et, lorsqu’elle est appelée, elle ne requiert pas de parenthèses.</span><span class="sxs-lookup"><span data-stu-id="06f82-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="06f82-417"><sup>3</sup> deux fonctions sont composables si la sortie d’une fonction peut être l’entrée de l’autre fonction.</span><span class="sxs-lookup"><span data-stu-id="06f82-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="06f82-418">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément de **fonction** .</span><span class="sxs-lookup"><span data-stu-id="06f82-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="06f82-419">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-420">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-421">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-421">Example</span></span>

<span data-ttu-id="06f82-422">L’exemple suivant montre un élément **Function** qui correspond à la procédure stockée **UpdateOrderQuantity** .</span><span class="sxs-lookup"><span data-stu-id="06f82-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="06f82-423">La procédure stockée accepte deux paramètres et ne retourne pas de valeur.</span><span class="sxs-lookup"><span data-stu-id="06f82-423">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="06f82-424">Key, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-424">Key Element (SSDL)</span></span>

<span data-ttu-id="06f82-425">L’élément **clé** en Store Schema Definition Language (SSDL) représente la clé primaire d’une table dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="06f82-426">**Key** est un élément enfant d’un élément EntityType, qui représente une ligne dans une table.</span><span class="sxs-lookup"><span data-stu-id="06f82-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="06f82-427">La clé primaire est définie dans l’élément **Key** en référençant un ou plusieurs éléments Property définis sur l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="06f82-428">L’élément **Key** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-429">PropertyRef (un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="06f82-430">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="06f82-430">Annotation elements</span></span>

<span data-ttu-id="06f82-431">Aucun attribut n’est applicable à l’élément **Key** .</span><span class="sxs-lookup"><span data-stu-id="06f82-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-432">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-432">Example</span></span>

<span data-ttu-id="06f82-433">L’exemple suivant montre un élément **EntityType** avec une clé qui référence une propriété :</span><span class="sxs-lookup"><span data-stu-id="06f82-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="06f82-434">OnDelete, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="06f82-435">L’élément **OnDelete** en Store Schema Definition Language (SSDL) reflète le comportement de la base de données lorsqu’une ligne qui participe à une contrainte de clé étrangère est supprimée.</span><span class="sxs-lookup"><span data-stu-id="06f82-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="06f82-436">Si l’action est définie sur **cascade**, les lignes qui font référence à une ligne en cours de suppression seront également supprimées.</span><span class="sxs-lookup"><span data-stu-id="06f82-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="06f82-437">Si l’action est définie sur **aucun**, les lignes qui font référence à une ligne en cours de suppression ne sont pas supprimées.</span><span class="sxs-lookup"><span data-stu-id="06f82-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="06f82-438">Un élément **OnDelete** est un élément enfant d’un élément end.</span><span class="sxs-lookup"><span data-stu-id="06f82-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="06f82-439">Un élément **OnDelete** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-440">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-441">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-442">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-442">Applicable Attributes</span></span>

<span data-ttu-id="06f82-443">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="06f82-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="06f82-444">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-444">Attribute Name</span></span> | <span data-ttu-id="06f82-445">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-445">Is Required</span></span> | <span data-ttu-id="06f82-446">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-447">**Action**</span><span class="sxs-lookup"><span data-stu-id="06f82-447">**Action**</span></span>     | <span data-ttu-id="06f82-448">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-448">Yes</span></span>         | <span data-ttu-id="06f82-449">**Cascade** ou **None**.</span><span class="sxs-lookup"><span data-stu-id="06f82-449">**Cascade** or **None**.</span></span> <span data-ttu-id="06f82-450">(La valeur **Restricted** est valide mais a le même comportement qu' **aucun**.)</span><span class="sxs-lookup"><span data-stu-id="06f82-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-451">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="06f82-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="06f82-452">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-453">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-454">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-454">Example</span></span>

<span data-ttu-id="06f82-455">L’exemple suivant montre un élément **Association** qui définit la contrainte de clé étrangère **FK @ no__t-2CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="06f82-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="06f82-456">L’élément **OnDelete** indique que toutes les lignes de la table **Orders** qui font référence à une ligne particulière de la table **Customers** seront supprimées si la ligne de la table **Customers** est supprimée.</span><span class="sxs-lookup"><span data-stu-id="06f82-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="06f82-457">Élément Parameter (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="06f82-458">L’élément **Parameter** en Store Schema Definition Language (SSDL) est un enfant de l’élément Function qui spécifie les paramètres d’une procédure stockée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="06f82-459">L’élément **Parameter** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-460">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-461">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-462">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-462">Applicable Attributes</span></span>

<span data-ttu-id="06f82-463">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="06f82-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="06f82-464">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-464">Attribute Name</span></span> | <span data-ttu-id="06f82-465">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-465">Is Required</span></span> | <span data-ttu-id="06f82-466">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-467">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-467">**Name**</span></span>       | <span data-ttu-id="06f82-468">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-468">Yes</span></span>         | <span data-ttu-id="06f82-469">Nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="06f82-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="06f82-470">**Type**</span><span class="sxs-lookup"><span data-stu-id="06f82-470">**Type**</span></span>       | <span data-ttu-id="06f82-471">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-471">Yes</span></span>         | <span data-ttu-id="06f82-472">Type du paramètre.</span><span class="sxs-lookup"><span data-stu-id="06f82-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="06f82-473">**Mode**</span><span class="sxs-lookup"><span data-stu-id="06f82-473">**Mode**</span></span>       | <span data-ttu-id="06f82-474">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-474">No</span></span>          | <span data-ttu-id="06f82-475">**In**, **out**ou **INOUT** selon que le paramètre est un paramètre d’entrée, de sortie ou d’entrée/sortie.</span><span class="sxs-lookup"><span data-stu-id="06f82-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="06f82-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="06f82-476">**MaxLength**</span></span>  | <span data-ttu-id="06f82-477">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-477">No</span></span>          | <span data-ttu-id="06f82-478">Longueur maximale du paramètre.</span><span class="sxs-lookup"><span data-stu-id="06f82-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="06f82-479">**Précision**</span><span class="sxs-lookup"><span data-stu-id="06f82-479">**Precision**</span></span>  | <span data-ttu-id="06f82-480">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-480">No</span></span>          | <span data-ttu-id="06f82-481">Précision du paramètre.</span><span class="sxs-lookup"><span data-stu-id="06f82-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="06f82-482">**Échelle**</span><span class="sxs-lookup"><span data-stu-id="06f82-482">**Scale**</span></span>      | <span data-ttu-id="06f82-483">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-483">No</span></span>          | <span data-ttu-id="06f82-484">Échelle du paramètre.</span><span class="sxs-lookup"><span data-stu-id="06f82-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="06f82-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="06f82-485">**SRID**</span></span>       | <span data-ttu-id="06f82-486">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-486">No</span></span>          | <span data-ttu-id="06f82-487">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="06f82-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="06f82-488">Valide uniquement pour les paramètres des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="06f82-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="06f82-489">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="06f82-489">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-490">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="06f82-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="06f82-491">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-492">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-493">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-493">Example</span></span>

<span data-ttu-id="06f82-494">L’exemple suivant montre un élément **Function** qui possède deux éléments **Parameter** qui spécifient des paramètres d’entrée :</span><span class="sxs-lookup"><span data-stu-id="06f82-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="06f82-495">Élément Principal (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="06f82-496">L’élément **principal** en Store Schema Definition Language (SSDL) est un élément enfant de l’élément ReferentialConstraint qui définit la terminaison principale d’une contrainte de clé étrangère (également appelée contrainte référentielle).</span><span class="sxs-lookup"><span data-stu-id="06f82-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="06f82-497">L’élément **principal** spécifie la ou les colonnes de clé primaire d’une table qui est référencée par une ou plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="06f82-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="06f82-498">Les éléments **PropertyRef** spécifient les colonnes qui sont référencées.</span><span class="sxs-lookup"><span data-stu-id="06f82-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="06f82-499">L’élément dépendant spécifie les colonnes qui référencent les colonnes de clés primaires spécifiées dans l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="06f82-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="06f82-500">L’élément **principal** peut avoir les éléments enfants suivants (dans l’ordre indiqué) :</span><span class="sxs-lookup"><span data-stu-id="06f82-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="06f82-501">PropertyRef (un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="06f82-502">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-503">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-503">Applicable Attributes</span></span>

<span data-ttu-id="06f82-504">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="06f82-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="06f82-505">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-505">Attribute Name</span></span> | <span data-ttu-id="06f82-506">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-506">Is Required</span></span> | <span data-ttu-id="06f82-507">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-508">**Rôle**</span><span class="sxs-lookup"><span data-stu-id="06f82-508">**Role**</span></span>       | <span data-ttu-id="06f82-509">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-509">Yes</span></span>         | <span data-ttu-id="06f82-510">La même valeur que l’attribut de **rôle** (s’il est utilisé) de l’élément de fin correspondant ; Sinon, le nom de la table qui contient la colonne référencée.</span><span class="sxs-lookup"><span data-stu-id="06f82-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-511">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **principal** .</span><span class="sxs-lookup"><span data-stu-id="06f82-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="06f82-512">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-513">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-514">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-514">Example</span></span>

<span data-ttu-id="06f82-515">L’exemple suivant montre un élément Association qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK @ no__t-2CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="06f82-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="06f82-516">L’élément **principal** spécifie la colonne **CustomerID** de la table **Customer** comme terminaison principale de la contrainte.</span><span class="sxs-lookup"><span data-stu-id="06f82-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="06f82-517">Property, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-517">Property Element (SSDL)</span></span>

<span data-ttu-id="06f82-518">L’élément de **propriété** en Store Schema Definition Language (SSDL) représente une colonne dans une table de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="06f82-519">Les éléments de **propriété** sont des enfants d’éléments EntityType, qui représentent des lignes dans une table.</span><span class="sxs-lookup"><span data-stu-id="06f82-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="06f82-520">Chaque élément de **propriété** défini sur un élément **EntityType** représente une colonne.</span><span class="sxs-lookup"><span data-stu-id="06f82-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="06f82-521">Un élément de **propriété** ne peut pas avoir d’éléments enfants.</span><span class="sxs-lookup"><span data-stu-id="06f82-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-522">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-522">Applicable Attributes</span></span>

<span data-ttu-id="06f82-523">Le tableau suivant décrit les attributs qui peuvent être appliqués à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="06f82-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="06f82-524">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-524">Attribute Name</span></span>            | <span data-ttu-id="06f82-525">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-525">Is Required</span></span> | <span data-ttu-id="06f82-526">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-527">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-527">**Name**</span></span>                  | <span data-ttu-id="06f82-528">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-528">Yes</span></span>         | <span data-ttu-id="06f82-529">Nom de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="06f82-530">**Type**</span><span class="sxs-lookup"><span data-stu-id="06f82-530">**Type**</span></span>                  | <span data-ttu-id="06f82-531">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-531">Yes</span></span>         | <span data-ttu-id="06f82-532">Type de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="06f82-533">**Nullable**</span><span class="sxs-lookup"><span data-stu-id="06f82-533">**Nullable**</span></span>              | <span data-ttu-id="06f82-534">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-534">No</span></span>          | <span data-ttu-id="06f82-535">**True** (valeur par défaut) ou **false** selon que la colonne correspondante peut avoir une valeur null ou non.</span><span class="sxs-lookup"><span data-stu-id="06f82-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="06f82-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="06f82-536">**DefaultValue**</span></span>          | <span data-ttu-id="06f82-537">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-537">No</span></span>          | <span data-ttu-id="06f82-538">Valeur par défaut de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="06f82-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="06f82-539">**MaxLength**</span></span>             | <span data-ttu-id="06f82-540">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-540">No</span></span>          | <span data-ttu-id="06f82-541">Longueur maximale de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="06f82-542">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="06f82-542">**FixedLength**</span></span>           | <span data-ttu-id="06f82-543">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-543">No</span></span>          | <span data-ttu-id="06f82-544">**True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne de longueur fixe.</span><span class="sxs-lookup"><span data-stu-id="06f82-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="06f82-545">**Précision**</span><span class="sxs-lookup"><span data-stu-id="06f82-545">**Precision**</span></span>             | <span data-ttu-id="06f82-546">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-546">No</span></span>          | <span data-ttu-id="06f82-547">Précision de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="06f82-548">**Échelle**</span><span class="sxs-lookup"><span data-stu-id="06f82-548">**Scale**</span></span>                 | <span data-ttu-id="06f82-549">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-549">No</span></span>          | <span data-ttu-id="06f82-550">Échelle de la colonne correspondante.</span><span class="sxs-lookup"><span data-stu-id="06f82-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="06f82-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="06f82-551">**Unicode**</span></span>               | <span data-ttu-id="06f82-552">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-552">No</span></span>          | <span data-ttu-id="06f82-553">**True** ou **false** selon que la valeur de colonne correspondante sera stockée en tant que chaîne Unicode.</span><span class="sxs-lookup"><span data-stu-id="06f82-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="06f82-554">**Classement**</span><span class="sxs-lookup"><span data-stu-id="06f82-554">**Collation**</span></span>             | <span data-ttu-id="06f82-555">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-555">No</span></span>          | <span data-ttu-id="06f82-556">Chaîne qui spécifie l'ordre de tri à utiliser dans la source de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="06f82-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="06f82-557">**SRID**</span></span>                  | <span data-ttu-id="06f82-558">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-558">No</span></span>          | <span data-ttu-id="06f82-559">Identificateur de référence système spatial.</span><span class="sxs-lookup"><span data-stu-id="06f82-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="06f82-560">Valide uniquement pour les propriétés des types spatiaux.</span><span class="sxs-lookup"><span data-stu-id="06f82-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="06f82-561">Pour plus d’informations, consultez [SRID](https://en.wikipedia.org/wiki/SRID) et [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="06f82-561">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="06f82-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="06f82-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="06f82-563">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-563">No</span></span>          | <span data-ttu-id="06f82-564">**None**, **Identity** (si la valeur de colonne correspondante est une identité générée dans la base de données) ou **calculée** (si la valeur de colonne correspondante est calculée dans la base de données).</span><span class="sxs-lookup"><span data-stu-id="06f82-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="06f82-565">Non valide pour les propriétés RowType.</span><span class="sxs-lookup"><span data-stu-id="06f82-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-566">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **Property** .</span><span class="sxs-lookup"><span data-stu-id="06f82-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="06f82-567">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-568">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-569">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-569">Example</span></span>

<span data-ttu-id="06f82-570">L’exemple suivant montre un élément **EntityType** avec deux éléments de **propriété** enfants :</span><span class="sxs-lookup"><span data-stu-id="06f82-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="06f82-571">Élément PropertyRef (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="06f82-572">L’élément **PropertyRef** en Store Schema Definition Language (SSDL) fait référence à une propriété définie sur un élément EntityType pour indiquer que la propriété effectuera l’un des rôles suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="06f82-573">Faire partie de la clé primaire de la table représentée par l' **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="06f82-574">Un ou plusieurs éléments **PropertyRef** peuvent être utilisés pour définir une clé primaire.</span><span class="sxs-lookup"><span data-stu-id="06f82-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="06f82-575">Pour plus d’informations, consultez Key, élément.</span><span class="sxs-lookup"><span data-stu-id="06f82-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="06f82-576">Faire partie de la terminaison dépendante ou principale d'une contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="06f82-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="06f82-577">Pour plus d’informations, consultez ReferentialConstraint, élément.</span><span class="sxs-lookup"><span data-stu-id="06f82-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="06f82-578">L’élément **PropertyRef** ne peut avoir que les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="06f82-579">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-580">éléments d'annotation.</span><span class="sxs-lookup"><span data-stu-id="06f82-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-581">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-581">Applicable Attributes</span></span>

<span data-ttu-id="06f82-582">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="06f82-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="06f82-583">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-583">Attribute Name</span></span> | <span data-ttu-id="06f82-584">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-584">Is Required</span></span> | <span data-ttu-id="06f82-585">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="06f82-586">**Nom**</span><span class="sxs-lookup"><span data-stu-id="06f82-586">**Name**</span></span>       | <span data-ttu-id="06f82-587">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-587">Yes</span></span>         | <span data-ttu-id="06f82-588">Nom de la propriété référencée.</span><span class="sxs-lookup"><span data-stu-id="06f82-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="06f82-589">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="06f82-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="06f82-590">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="06f82-591">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-592">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-592">Example</span></span>

<span data-ttu-id="06f82-593">L’exemple suivant montre un élément **PropertyRef** utilisé pour définir une clé primaire en référençant une propriété définie sur un élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="06f82-594">ReferentialConstraint, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="06f82-595">L’élément **ReferentialConstraint** en Store Schema Definition Language (SSDL) représente une contrainte de clé étrangère (également appelée contrainte d’intégrité référentielle) dans la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="06f82-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="06f82-596">Les terminaisons principale et dépendante de la contrainte sont spécifiées par les éléments enfants principaux et dépendants, respectivement.</span><span class="sxs-lookup"><span data-stu-id="06f82-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="06f82-597">Les colonnes qui participent au principal et les terminaisons dépendantes sont référencées avec des éléments PropertyRef.</span><span class="sxs-lookup"><span data-stu-id="06f82-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="06f82-598">L’élément **ReferentialConstraint** est un élément enfant facultatif de l’élément Association.</span><span class="sxs-lookup"><span data-stu-id="06f82-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="06f82-599">Si un élément **ReferentialConstraint** n’est pas utilisé pour mapper la contrainte de clé étrangère spécifiée dans l’élément **Association** , un élément AssociationSetMapping doit être utilisé pour ce faire.</span><span class="sxs-lookup"><span data-stu-id="06f82-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="06f82-600">L’élément **ReferentialConstraint** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="06f82-601">Documentation (zéro ou un)</span><span class="sxs-lookup"><span data-stu-id="06f82-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="06f82-602">Principal (exactement un)</span><span class="sxs-lookup"><span data-stu-id="06f82-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="06f82-603">Dépendant (exactement un)</span><span class="sxs-lookup"><span data-stu-id="06f82-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="06f82-604">Éléments d’annotation (zéro ou plus)</span><span class="sxs-lookup"><span data-stu-id="06f82-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-605">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-605">Applicable Attributes</span></span>

<span data-ttu-id="06f82-606">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="06f82-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="06f82-607">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-608">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-609">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-609">Example</span></span>

<span data-ttu-id="06f82-610">L’exemple suivant montre un élément **Association** qui utilise un élément **ReferentialConstraint** pour spécifier les colonnes qui participent à la contrainte de clé étrangère **FK @ no__t-3CustomerOrders** :</span><span class="sxs-lookup"><span data-stu-id="06f82-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="06f82-611">ReturnType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="06f82-612">L’élément **ReturnType** en Store Schema Definition Language (SSDL) spécifie le type de retour pour une fonction définie dans un élément **Function** .</span><span class="sxs-lookup"><span data-stu-id="06f82-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="06f82-613">Un type de retour de fonction peut également être spécifié avec un attribut **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="06f82-614">Le type de retour d’une fonction est spécifié à l’aide de l’attribut **type** ou de l’élément **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="06f82-615">L’élément **ReturnType** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="06f82-616">CollectionType (un)</span><span class="sxs-lookup"><span data-stu-id="06f82-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="06f82-617">Un nombre quelconque d’attributs d’annotation (attributs XML personnalisés) peut être appliqué à l’élément **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="06f82-618">Toutefois, les attributs personnalisés ne peuvent pas appartenir à un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="06f82-619">Les noms qualifiés complets de deux attributs personnalisés quelconques ne peuvent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-620">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-620">Example</span></span>

<span data-ttu-id="06f82-621">L’exemple suivant utilise une **fonction** qui retourne une collection de lignes.</span><span class="sxs-lookup"><span data-stu-id="06f82-621">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="06f82-622">RowType, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="06f82-623">Un élément **RowType** en Store Schema Definition Language (SSDL) définit une structure sans nom comme type de retour pour une fonction définie dans le magasin.</span><span class="sxs-lookup"><span data-stu-id="06f82-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="06f82-624">Un élément **RowType** est l’élément enfant de l’élément **CollectionType** :</span><span class="sxs-lookup"><span data-stu-id="06f82-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="06f82-625">Un élément **RowType** peut avoir les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="06f82-626">Property (un ou plusieurs)</span><span class="sxs-lookup"><span data-stu-id="06f82-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="06f82-627">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-627">Example</span></span>

<span data-ttu-id="06f82-628">L’exemple suivant montre une fonction de magasin qui utilise un élément **CollectionType** pour spécifier que la fonction retourne une collection de lignes (comme spécifié dans l’élément **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="06f82-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="06f82-629">Schema, élément (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="06f82-630">L’élément **Schema** en Store Schema Definition Language (SSDL) est l’élément racine d’une définition de modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="06f82-631">Il contient des définitions pour les objets, les fonctions et les conteneurs qui composent un modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="06f82-632">L’élément **Schema** peut contenir zéro, un ou plusieurs des éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="06f82-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="06f82-633">Association</span><span class="sxs-lookup"><span data-stu-id="06f82-633">Association</span></span>
-   <span data-ttu-id="06f82-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="06f82-634">EntityType</span></span>
-   <span data-ttu-id="06f82-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="06f82-635">EntityContainer</span></span>
-   <span data-ttu-id="06f82-636">Fonction</span><span class="sxs-lookup"><span data-stu-id="06f82-636">Function</span></span>

<span data-ttu-id="06f82-637">L’élément **Schema** utilise l’attribut **namespace** pour définir l’espace de noms du type d’entité et des objets Association dans un modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="06f82-638">Dans un espace de noms, deux objets ne peuvent pas avoir le même nom.</span><span class="sxs-lookup"><span data-stu-id="06f82-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="06f82-639">Un espace de noms de modèle de stockage est différent de l’espace de noms XML de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="06f82-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="06f82-640">Un espace de noms de modèle de stockage (tel que défini par l’attribut d' **espace de noms** ) est un conteneur logique pour les types d’entités et les types d’association.</span><span class="sxs-lookup"><span data-stu-id="06f82-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="06f82-641">L’espace de noms XML (indiqué par l’attribut **xmlns** ) d’un élément de **schéma** est l’espace de noms par défaut pour les éléments enfants et les attributs de l’élément de **schéma** .</span><span class="sxs-lookup"><span data-stu-id="06f82-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="06f82-642">Les espaces de noms XML de la forme https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (où aaaa et MM représentent respectivement une année et un mois) sont réservés au langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-642">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="06f82-643">Des éléments et attributs personnalisés ne peuvent pas être dans des espaces de noms de cette forme.</span><span class="sxs-lookup"><span data-stu-id="06f82-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="06f82-644">Attributs applicables</span><span class="sxs-lookup"><span data-stu-id="06f82-644">Applicable Attributes</span></span>

<span data-ttu-id="06f82-645">Le tableau ci-dessous décrit les attributs qui peuvent être appliqués à l’élément **Schema** .</span><span class="sxs-lookup"><span data-stu-id="06f82-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="06f82-646">Nom d'attribut</span><span class="sxs-lookup"><span data-stu-id="06f82-646">Attribute Name</span></span>            | <span data-ttu-id="06f82-647">Requis</span><span class="sxs-lookup"><span data-stu-id="06f82-647">Is Required</span></span> | <span data-ttu-id="06f82-648">Value</span><span class="sxs-lookup"><span data-stu-id="06f82-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-649">**Espace de noms**</span><span class="sxs-lookup"><span data-stu-id="06f82-649">**Namespace**</span></span>             | <span data-ttu-id="06f82-650">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-650">Yes</span></span>         | <span data-ttu-id="06f82-651">Espace de noms du modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-651">The namespace of the storage model.</span></span> <span data-ttu-id="06f82-652">La valeur de l’attribut d' **espace de noms** est utilisée pour former le nom qualifié complet d’un type.</span><span class="sxs-lookup"><span data-stu-id="06f82-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="06f82-653">Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store, le nom qualifié complet de l' **EntityType** est ExampleModel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="06f82-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="06f82-654">Les chaînes suivantes ne peuvent pas être utilisées comme valeur pour l’attribut d' **espace de noms** : **Système**, **transitoire**ou **EDM**.</span><span class="sxs-lookup"><span data-stu-id="06f82-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="06f82-655">La valeur de l’attribut d' **espace de noms** ne peut pas être la même que la valeur de l’attribut d' **espace de noms** dans l’élément de schéma CSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="06f82-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="06f82-656">**Alias**</span></span>                 | <span data-ttu-id="06f82-657">Non</span><span class="sxs-lookup"><span data-stu-id="06f82-657">No</span></span>          | <span data-ttu-id="06f82-658">Identificateur utilisé à la place du nom de l'espace de noms.</span><span class="sxs-lookup"><span data-stu-id="06f82-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="06f82-659">Par exemple, si un **EntityType** nommé *Customer* se trouve dans l’espace de noms ExampleModel. Store et que la valeur de l’attribut **alias** est *StorageModel*, vous pouvez utiliser StorageModel. Customer comme nom qualifié complet de la  **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="06f82-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="06f82-660">**Fournisseur**</span><span class="sxs-lookup"><span data-stu-id="06f82-660">**Provider**</span></span>              | <span data-ttu-id="06f82-661">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-661">Yes</span></span>         | <span data-ttu-id="06f82-662">Fournisseur de données.</span><span class="sxs-lookup"><span data-stu-id="06f82-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="06f82-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="06f82-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="06f82-664">Oui</span><span class="sxs-lookup"><span data-stu-id="06f82-664">Yes</span></span>         | <span data-ttu-id="06f82-665">Jeton qui indique au fournisseur quel manifeste de fournisseur retourner.</span><span class="sxs-lookup"><span data-stu-id="06f82-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="06f82-666">Aucun format n'est défini pour le jeton.</span><span class="sxs-lookup"><span data-stu-id="06f82-666">No format for the token is defined.</span></span> <span data-ttu-id="06f82-667">Les valeurs du jeton sont définies par le fournisseur.</span><span class="sxs-lookup"><span data-stu-id="06f82-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="06f82-668">Pour plus d’informations sur les jetons de manifeste du fournisseur SQL Server, consultez SqlClient pour Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="06f82-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="06f82-669">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-669">Example</span></span>

<span data-ttu-id="06f82-670">L’exemple suivant illustre un élément de **schéma** qui contient un élément **EntityContainer** , deux éléments **EntityType** et un élément **Association** .</span><span class="sxs-lookup"><span data-stu-id="06f82-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="06f82-671">Attributs d'annotation</span><span class="sxs-lookup"><span data-stu-id="06f82-671">Annotation Attributes</span></span>

<span data-ttu-id="06f82-672">Les attributs d'annotation en SSDL (Store Schema Definition Language) sont des attributs XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant les éléments dans le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="06f82-673">En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux attributs d'annotation :</span><span class="sxs-lookup"><span data-stu-id="06f82-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="06f82-674">Les attributs d'annotation ne doivent pas figurer dans un espace de noms XML réservé pour le langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="06f82-675">Les noms qualifiés complets de deux attributs d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="06f82-676">Plusieurs attributs d'annotation peuvent être appliqués à un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="06f82-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="06f82-677">Vous pouvez accéder aux métadonnées contenues dans les éléments d’annotation au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="06f82-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-678">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-678">Example</span></span>

<span data-ttu-id="06f82-679">L’exemple suivant montre un élément EntityType qui a un attribut d’annotation appliqué à la propriété **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="06f82-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="06f82-680">L’exemple montre également un élément d’annotation ajouté à l’élément **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="06f82-680">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="06f82-681">Éléments d'annotation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="06f82-682">Les éléments d'annotation en SSDL (Store Schema Definition Language) sont des éléments XML personnalisés dans le modèle de stockage qui fournissent des métadonnées supplémentaires concernant le modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="06f82-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="06f82-683">En plus d'avoir une structure XML valide, les contraintes suivantes s'appliquent aux éléments d'annotation :</span><span class="sxs-lookup"><span data-stu-id="06f82-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="06f82-684">Les éléments d'annotation ne doivent pas figurer dans un espace de noms XML réservé au langage SSDL.</span><span class="sxs-lookup"><span data-stu-id="06f82-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="06f82-685">Les noms qualifiés complets de deux éléments d'annotation ne doivent pas être identiques.</span><span class="sxs-lookup"><span data-stu-id="06f82-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="06f82-686">Les éléments d'annotation doivent apparaître après tous les autres éléments enfants d'un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="06f82-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="06f82-687">Plusieurs éléments d'annotation peuvent être des enfants d'un élément SSDL donné.</span><span class="sxs-lookup"><span data-stu-id="06f82-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="06f82-688">À partir de la .NET Framework version 4, les métadonnées contenues dans les éléments d’annotation sont accessibles au moment de l’exécution à l’aide des classes de l’espace de noms System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="06f82-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="06f82-689">Exemple</span><span class="sxs-lookup"><span data-stu-id="06f82-689">Example</span></span>

<span data-ttu-id="06f82-690">L’exemple suivant montre un élément EntityType qui a un élément annotation (**customelement**).</span><span class="sxs-lookup"><span data-stu-id="06f82-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="06f82-691">L’exemple montre également un attribut d’annotation appliqué à la propriété **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="06f82-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="06f82-692">Facettes (SSDL)</span><span class="sxs-lookup"><span data-stu-id="06f82-692">Facets (SSDL)</span></span>

<span data-ttu-id="06f82-693">Les facettes en Store Schema Definition Language (SSDL) représentent des contraintes sur les types de colonnes spécifiés dans les éléments de propriété.</span><span class="sxs-lookup"><span data-stu-id="06f82-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="06f82-694">Les facettes sont implémentées en tant qu’attributs XML sur les éléments de **propriété** .</span><span class="sxs-lookup"><span data-stu-id="06f82-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="06f82-695">Le tableau ci-dessous décrit les facettes prises en charge dans le langage SSDL :</span><span class="sxs-lookup"><span data-stu-id="06f82-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="06f82-696">Facette</span><span class="sxs-lookup"><span data-stu-id="06f82-696">Facet</span></span>           | <span data-ttu-id="06f82-697">Description</span><span class="sxs-lookup"><span data-stu-id="06f82-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="06f82-698">**Classement**</span><span class="sxs-lookup"><span data-stu-id="06f82-698">**Collation**</span></span>   | <span data-ttu-id="06f82-699">Spécifie la table de classement ou ordre de tri à utiliser lors de l'exécution d'opérations de comparaison et de tri sur des valeurs de la propriété.</span><span class="sxs-lookup"><span data-stu-id="06f82-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="06f82-700">**Multiple**</span><span class="sxs-lookup"><span data-stu-id="06f82-700">**FixedLength**</span></span> | <span data-ttu-id="06f82-701">Spécifie si la longueur de la valeur de colonne peut varier.</span><span class="sxs-lookup"><span data-stu-id="06f82-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="06f82-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="06f82-702">**MaxLength**</span></span>   | <span data-ttu-id="06f82-703">Spécifie la longueur maximale de la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="06f82-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="06f82-704">**Précision**</span><span class="sxs-lookup"><span data-stu-id="06f82-704">**Precision**</span></span>   | <span data-ttu-id="06f82-705">Pour les propriétés de type **Decimal**, spécifie le nombre de chiffres qu’une valeur de propriété peut avoir.</span><span class="sxs-lookup"><span data-stu-id="06f82-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="06f82-706">Pour les propriétés de type **Time**, **DateTime**et **DateTimeOffset**, spécifie le nombre de chiffres pour la partie fractionnaire des secondes de la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="06f82-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="06f82-707">**Échelle**</span><span class="sxs-lookup"><span data-stu-id="06f82-707">**Scale**</span></span>       | <span data-ttu-id="06f82-708">Spécifie le nombre de chiffres à droite de la virgule décimale pour la valeur de colonne.</span><span class="sxs-lookup"><span data-stu-id="06f82-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="06f82-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="06f82-709">**Unicode**</span></span>     | <span data-ttu-id="06f82-710">Indique si la valeur de colonne est stockée au format Unicode.</span><span class="sxs-lookup"><span data-stu-id="06f82-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
