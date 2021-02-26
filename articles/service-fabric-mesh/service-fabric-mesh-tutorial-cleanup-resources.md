---
title: 'Tutorial: Bereinigen von Azure Service Fabric Mesh-Ressourcen'
description: Hier erfahren Sie, wie Sie Azure Service Fabric Mesh-Ressourcen entfernen, damit keine Gebühren für nicht mehr verwendete Ressourcen anfallen.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d78e1e27d70b147bb52dff13675e63b79335d62
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625464"
---
# <a name="tutorial-remove-azure-resources"></a>Tutorial: Entfernen von Azure-Ressourcen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der fünfte Teil einer Serie. Hier wird gezeigt, wie Sie die App und ihre Ressourcen löschen, damit sie Ihnen nicht in Rechnung gestellt werden.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Bereinigen Sie die von der App verwendeten Ressourcen, damit keine Gebühren dafür anfallen.

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer Service Fabric-App in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debuggen einer Service Fabric Mesh-App, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Bereitstellen einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aktualisieren einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-upgrade.md)
> * Bereinigen von Service Fabric Mesh-Ressourcen

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie die To-Do-App noch nicht bereitgestellt haben, befolgen Sie die Anweisungen unter [Bereitstellen einer Service Fabric Mesh-Anwendung](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dies ist das Ende des Tutorials. Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit keine Gebühren für nicht mehr verwendete Ressourcen anfallen. Dies ist besonders wichtig, da Mesh ein serverloser Dienst mit sekundengenauer Abrechnung ist. Weitere Informationen zu den Preisen von Mesh finden Sie unter https://aka.ms/sfmeshpricing.

Azure bietet unter anderem den Vorteil, dass beim Löschen einer Ressourcengruppe alle erstellten Ressourcen gelöscht werden, die dieser Ressourcengruppe zugeordnet wurden. Sie müssen sie also nicht einzeln löschen.

Da Sie eine neue Ressourcengruppe zum Erstellen der To-Do-App erstellt haben, können Sie diese Ressourcengruppe bedenkenlos löschen. Hierbei werden alle zugehörigen Ressourcen gelöscht.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternativ können Sie die Ressourcengruppe **sfmeshTutorial1RG** über das [Portal](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups) löschen. 

## <a name="next-steps"></a>Nächste Schritte

Da Sie die Veröffentlichung einer Service Fabric Mesh-Anwendung in Azure nun abgeschlossen haben, versuchen Sie Folgendes:

* Untersuchen Sie das [Beispiel für eine Abstimmungs-App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp), um sich mit einem weiteren Beispiel für die Kommunikation zwischen Diensten vertraut zu machen.
* Weitere Informationen zum Service Fabric-Ressourcenmodell finden Sie unter [Service Fabric Mesh-Ressourcenmodell](service-fabric-mesh-service-fabric-resources.md).
* In der [Übersicht über Azure Service Fabric Mesh](service-fabric-mesh-overview.md) erfahren Sie mehr über Service Fabric Mesh.
* Erfahren Sie mehr über die [Cloud Shell](../cloud-shell/overview.md).
