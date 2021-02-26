---
title: 'Tutorial: Skalieren einer in Azure Service Fabric Mesh ausgeführten App'
description: In diesem Tutorial erfahren Sie, wie die Dienste in einer Anwendung skaliert werden, die in Service Fabric Mesh ausgeführt wird.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625382"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Tutorial: Skalieren einer in Service Fabric Mesh ausgeführten Anwendung

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der zweite Teil einer Reihe. Erfahren Sie, wie Sie die Anzahl der Dienstinstanzen einer Anwendung manuell skalieren, die [zuvor in Service Fabric Mesh bereitgestellt wurde](service-fabric-mesh-tutorial-template-deploy-app.md). Nach Abschluss des Tutorials verfügen Sie über einen Front-End-Dienst mit drei ausgeführten Instanzen sowie über einen Datendienst mit zwei ausgeführten Instanzen.

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der gewünschten Anzahl von Dienstinstanzen
> * Durchführen eines Upgrades

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Bereitstellen einer Anwendung in Service Fabric Mesh mithilfe einer Vorlage](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Skalieren einer in Service Fabric Mesh ausgeführten Anwendung
> * [Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Entfernen einer Anwendung](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* [Installieren Sie die Azure- und die Azure Service Fabric Mesh-Befehlszeilenschnittstelle lokal.](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)

## <a name="manually-scale-your-services-in-or-out"></a>Manuelles horizontales Hoch- und Herunterskalieren Ihrer Dienste

Einer der Hauptvorteile bei der Bereitstellung von Anwendungen in Service Fabric Mesh ist die Möglichkeit, Ihre Dienste ganz einfach horizontal hoch- und herunterzuskalieren. Auf diese Weise können Sie unterschiedlich starke Dienstauslastungen auffangen oder die Verfügbarkeit verbessern.

In diesem Tutorial wird die Beispiel-To-Do-Liste verwendet, die [zuvor bereitgestellt](service-fabric-mesh-tutorial-template-deploy-app.md) wurde und jetzt betriebsbereit sein sollte. Die Anwendung verfügt über zwei Dienste: WebFrontEnd und ToDoService. Jeder Dienst wurde ursprünglich mit einer Replikatanzahl von 1 bereitgestellt.  Um die Anzahl der ausgeführten Replikate für den WebFrontEnd-Dienst anzuzeigen, führen Sie Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Um die Anzahl der ausgeführten Replikate für den ToDoService-Dienst anzuzeigen, führen Sie Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

In der Bereitstellungsvorlage für die Anwendungsressource weist jeder Dienst eine Eigenschaft *replicaCount* auf, mit der Sie festlegen können, wie oft der Dienst bereitgestellt werden soll. Eine Anwendung kann aus mehreren zusammen bereitgestellten und verwalteten Diensten bestehen, wobei jeder Dienst eine eindeutige *replicaCount*-Zahl aufweist. Um die Anzahl von Dienstreplikaten zu skalieren, ändern Sie für jeden Dienst, der skaliert werden soll, den *replicaCount*-Wert in der Bereitstellungsvorlage oder in der Parameterdatei.  Führen Sie anschließend ein Upgrade der Anwendung durch.

### <a name="modify-the-deployment-template-parameters"></a>Ändern der Bereitstellungsvorlagenparameter

Wenn Ihre Vorlage Werte enthält, die Sie nach Bereitstellen der Anwendung wahrscheinlich ändern werden, oder wenn Sie gerne die Möglichkeit hätten, die Werte pro Bereitstellung zu ändern (falls diese Vorlage für andere Bereitstellungen wiederverwendet werden soll), ist es empfehlenswert, die Werte zu parametrisieren.

Zuvor wurde die Anwendung mit der [Bereitstellungsvorlage „mesh_rp.windows.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) und der [Parameterdatei „mesh_rp.windows.parameter.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) bereitgestellt.

Öffnen Sie die [Parameterdatei „mesh_rp.windows.parameter.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) lokal, und legen Sie den Wert *frontEndReplicaCount* auf 3 und den Wert *serviceReplicaCount* auf 2 fest:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Speichern Sie Ihre Änderungen an der Parameterdatei.  Die Parameter *frontEndReplicaCount* und *serviceReplicaCount* werden im Abschnitt *parameters* der [Bereitstellungsvorlage „mesh_rp.windows.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) deklariert:

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

Die Eigenschaft *replicaCount* des WebFrontEnd-Diensts verweist auf den Parameter *frontEndReplicaCount*, und die Eigenschaft *replicaCount* des ToDoService-Diensts verweist auf den Parameter *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Führen Sie nach dem Ändern der Vorlage ein Upgrade Ihrer Anwendung durch.

### <a name="upgrade-your-application"></a>Upgraden Ihrer Anwendung

Während die Anwendung ausgeführt wird, können Sie sie durch erneute Bereitstellung der Vorlage und der aktualisierten Parameterdatei aktualisieren:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Dadurch wird ein paralleles Upgrade für Ihre Anwendung gestartet, und die Dienstinstanzen sollten in wenigen Minuten erhöht werden.  Um die Anzahl der ausgeführten Replikate für den WebFrontEnd-Dienst anzuzeigen, führen Sie Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Um die Anzahl der ausgeführten Replikate für den ToDoService-Dienst anzuzeigen, führen Sie Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der gewünschten Anzahl von Dienstinstanzen
> * Durchführen eines Upgrades

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-upgrade-app.md)
