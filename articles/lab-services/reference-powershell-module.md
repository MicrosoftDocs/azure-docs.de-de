---
title: PowerShell-Modul für Azure Lab Services | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu einem PowerShell-Modul, das die Verwaltung von Artefakten in Azure Lab Services unterstützt.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646524"
---
# <a name="azlabservices-powershell-module-preview"></a>PowerShell-Modul „Az.LabServices“ (Vorschauversion)
Az.LabServices ist ein PowerShell-Modul, das die Verwaltung von Azure Lab Services vereinfacht. Das Modul enthält kombinierbare Funktionen zum Erstellen, Abfragen, Aktualisieren und Löschen von Lab-Konten, Labs, VMs und Images. Weitere Informationen zu diesem Modul finden Sie auf der [Az.LabServices-Startseite auf GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Dieses Modul befindet sich in der **Vorschau**. 

## <a name="example-command"></a>Beispielbefehl
Im Folgenden finden Sie ein Beispiel für die Verwendung eines PowerShell-Befehls, um alle aktuell ausgeführten VMs in allen Labs zu beenden.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Erste Schritte
1. Installieren Sie [Azure PowerShell](/powershell/azure/), wenn dies auf Ihrem Computer noch nicht geschehen ist. 
2. Laden Sie [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) auf Ihren Computer herunter.
3. Importieren Sie das Modul:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Führen Sie den folgenden Befehl aus, um alle Labs in Ihrem Abonnement aufzulisten.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie auf der [Az.LabServices-Startseite auf GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).