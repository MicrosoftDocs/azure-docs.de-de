---
title: 'Azure PowerShell-Beispiele: Zurücksetzen von Azure Cloud Services (erweiterter Support)'
description: Beispielskripts zum Zurücksetzen einer Azure-Clouddienstbereitstellung mit erweitertem Support
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475319"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Zurücksetzen eines Azure-Clouddiensts (erweiterter Support) 
In diesen Beispielen werden verschiedene Möglichkeiten zum Zurücksetzen einer vorhandenen Azure-Clouddienstbereitstellung mit erweitertem Support behandelt.

## <a name="reimage-role-instances-of-cloud-service"></a>Durchführen eines Reimagings für Rolleninstanzen des Clouddiensts
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Mit diesem Befehl wird ein Reimaging für zwei Rolleninstanzen (ContosoFrontEnd_IN_0 und ContosoBackEnd_IN_1) des Clouddiensts „ContosoCS“ durchgeführt, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="reimage-all-roles-of-cloud-service"></a>Durchführen eines Reimagings für alle Rollen des Clouddiensts
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Mit diesem Befehl wird ein Reimaging für alle Rolleninstanzen des Clouddiensts „ContosoCS“ durchgeführt, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Durchführen eines Reimagings für eine einzelne Rolleninstanz des Clouddiensts
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Mit diesem Befehl wird ein Reimaging für die Rolleninstanz „ContosoFrontEnd_IN_0“ des Clouddiensts „ContosoCS“ durchgeführt, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="rebuild-role-instances-of-cloud-service"></a>Neuerstellen von Rolleninstanzen des Clouddiensts
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Mit diesem Befehl werden zwei Rolleninstanzen (ContosoFrontEnd_IN_0 und ContosoBackEnd_IN_1) des Clouddiensts „ContosoCS“ neu erstellt, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="rebuild-all-roles-of-cloud-service"></a>Neuerstellen aller Rollen des Clouddiensts
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Mit diesem Befehl werden alle Rolleninstanzen des Clouddiensts „ContosoCS“ durchgeführt, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="restart-role-instances-of-cloud-service"></a>Neustarten von Rolleninstanzen des Clouddiensts
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Mit diesem Befehl werden zwei Rolleninstanzen (ContosoFrontEnd_IN_0 und ContosoBackEnd_IN_1) des Clouddiensts „ContosoCS“ neu gestartet, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="restart-all-roles-of-cloud-service"></a>Neustarten aller Rollen des Clouddiensts
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Mit diesem Befehl werden alle Rolleninstanzen des Clouddiensts „ContosoCS“ neu gestartet, der zur Ressourcengruppe „ContosOrg“ gehört.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Cloud Services (erweiterter Support) finden Sie unter [Informationen zu Azure Cloud Services (erweiterter Support)](overview.md).
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
