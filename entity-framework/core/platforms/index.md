---
title: Implémentations de .NET prises en charge - EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434121"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="ed0b5-102">Implémentations de .NET prises en charge par EF Core</span><span class="sxs-lookup"><span data-stu-id="ed0b5-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="ed0b5-103">Nous souhaitons qu’EF Core soit disponible pour les développeurs sur toutes les implémentations de .NET modernes et nous travaillons toujours à cet objectif.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="ed0b5-104">Si la prise en charge d’EF Core sur .NET Core fait l’objet de tests automatisés et fonctionne correctement dans de nombreuses applications, Mono, Xamarin et UWP posent des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="ed0b5-105">Vue d'ensemble</span><span class="sxs-lookup"><span data-stu-id="ed0b5-105">Overview</span></span>

<span data-ttu-id="ed0b5-106">Le tableau suivant fournit des conseils pour chaque implémentation de .NET :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="ed0b5-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="ed0b5-107">EF Core</span></span>                       | <span data-ttu-id="ed0b5-108">2.1 et 3.1</span><span class="sxs-lookup"><span data-stu-id="ed0b5-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="ed0b5-109">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="ed0b5-109">.NET Standard</span></span>                 | <span data-ttu-id="ed0b5-110">2.0</span><span class="sxs-lookup"><span data-stu-id="ed0b5-110">2.0</span></span>         |
| <span data-ttu-id="ed0b5-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed0b5-111">.NET Core</span></span>                     | <span data-ttu-id="ed0b5-112">2.0</span><span class="sxs-lookup"><span data-stu-id="ed0b5-112">2.0</span></span>         |
| <span data-ttu-id="ed0b5-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="ed0b5-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="ed0b5-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="ed0b5-114">4.7.2</span></span>       |
| <span data-ttu-id="ed0b5-115">Mono</span><span class="sxs-lookup"><span data-stu-id="ed0b5-115">Mono</span></span>                          | <span data-ttu-id="ed0b5-116">5,4</span><span class="sxs-lookup"><span data-stu-id="ed0b5-116">5.4</span></span>         |
| <span data-ttu-id="ed0b5-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="ed0b5-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="ed0b5-118">10.14</span><span class="sxs-lookup"><span data-stu-id="ed0b5-118">10.14</span></span>       |
| <span data-ttu-id="ed0b5-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="ed0b5-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="ed0b5-120">8.0</span><span class="sxs-lookup"><span data-stu-id="ed0b5-120">8.0</span></span>         |
| <span data-ttu-id="ed0b5-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="ed0b5-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="ed0b5-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="ed0b5-122">10.0.16299</span></span>  |
| <span data-ttu-id="ed0b5-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="ed0b5-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="ed0b5-124">2018.1</span><span class="sxs-lookup"><span data-stu-id="ed0b5-124">2018.1</span></span>      |

<span data-ttu-id="ed0b5-125"><sup>(1)</sup> Consultez la section [.NET Framework](#net-framework) ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="ed0b5-126"><sup>(2)</sup> La présence de problèmes et de limitations connues avec Xamarin peut entraîner un dysfonctionnement de certaines applications développées à l’aide d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="ed0b5-127">Consultez la liste des [problèmes actifs](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) pour connaître les solutions de contournement.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="ed0b5-128"><sup>(3)</sup> EF Core 2.0.1 et versions ultérieures recommandées.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="ed0b5-129">Installez le [package .NET Core UWP 6.x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span><span class="sxs-lookup"><span data-stu-id="ed0b5-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="ed0b5-130">Consultez la section [Plateforme Windows universelle](#universal-windows-platform) de cet article.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="ed0b5-131"><sup>4</sup> Il existe des problèmes et des limitations connus avec Unity.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="ed0b5-132">Consultez la liste des [problèmes actifs](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span><span class="sxs-lookup"><span data-stu-id="ed0b5-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="ed0b5-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="ed0b5-133">.NET Framework</span></span>

<span data-ttu-id="ed0b5-134">Vous devez peut-être modifier les applications qui ciblent le .NET Framework pour qu’elle fonctionnent avec les bibliothèques .NET Standard :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="ed0b5-135">Modifiez le fichier projet et vérifiez que l’entrée suivante apparaît dans le groupe de propriétés initiales :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="ed0b5-136">Pour les projets de test, vérifiez également que l’entrée suivante est présente :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="ed0b5-137">Si vous voulez utiliser une ancienne version de Visual Studio, vous devez [mettre à niveau le client NuGet vers la version 3.6.0](https://www.nuget.org/downloads) pour utiliser les bibliothèques .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="ed0b5-138">Nous vous recommandons également d’effectuer une migration depuis NuGet packages.config vers PackageReference, si possible.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="ed0b5-139">Ajoutez la propriété suivante à votre fichier projet :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="ed0b5-140">Plateforme Windows universelle</span><span class="sxs-lookup"><span data-stu-id="ed0b5-140">Universal Windows Platform</span></span>

<span data-ttu-id="ed0b5-141">Les versions antérieures d’EF Core et de .NET UWP présentaient de nombreux problèmes de compatibilité, notamment avec les applications compilées avec la chaîne d’outils .NET Native.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="ed0b5-142">La nouvelle version de .NET UWP prend en charge .NET Standard 2.0 et contient .NET Native 2.0 qui résout la plupart des problèmes de compatibilité signalés précédemment.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="ed0b5-143">EF Core 2.0.1 a été testé de manière plus approfondie avec UWP, mais les tests ne sont pas automatisés.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="ed0b5-144">Avec EF Core sur UWP :</span><span class="sxs-lookup"><span data-stu-id="ed0b5-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="ed0b5-145">Pour optimiser les performances des requêtes, évitez les types anonymes dans les requêtes LINQ.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="ed0b5-146">Une application UWP doit être générée avec .NET Native pour pouvoir être déployée dans le magasin d’applications.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="ed0b5-147">Les requêtes avec des types anonymes ont des performances inférieures sur .NET Native.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="ed0b5-148">Pour optimiser les performances `SaveChanges()`, utilisez [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) et implémentez [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging ](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) et [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) dans vos types d’entité.</span><span class="sxs-lookup"><span data-stu-id="ed0b5-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="ed0b5-149">Signaler des problèmes</span><span class="sxs-lookup"><span data-stu-id="ed0b5-149">Report issues</span></span>

<span data-ttu-id="ed0b5-150">Pour toute combinaison ne fonctionnant pas comme prévu, nous vous invitons à signaler les nouveaux problèmes dans le [système de suivi des problèmes EF Core](https://github.com/aspnet/entityframeworkcore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="ed0b5-150">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="ed0b5-151">Pour les problèmes spécifiques à Xamarin, utilisez le système de suivi des problèmes pour [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) ou [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span><span class="sxs-lookup"><span data-stu-id="ed0b5-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
