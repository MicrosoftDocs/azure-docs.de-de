---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 807cbd283cf7971bf4256451028ffa16a0911266
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027815"
---
## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs 

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten. Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie mit [New-AzGallery](/powershell/module/az.compute/new-azgallery) einen Imagekatalog. Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>Teilen des Katalogs

Wir empfehlen, dass Sie den Zugriff auf der Ebene des Imagekatalogs teilen. Verwenden Sie eine E-Mail-Adresse und das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), um die Objekt-ID für den Benutzer abzurufen, und verwenden Sie dann [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment), um ihnen Zugriff auf den Katalog zu gewähren. Ersetzen Sie die Beispiel-E-Mail-Adresse alinne_montes@contoso.com in diesem Beispiel durch Ihre eigenen Informationen.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```