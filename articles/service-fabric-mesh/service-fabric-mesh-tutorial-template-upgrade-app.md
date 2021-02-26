---
title: 'Tutorial: Upgraden einer in Azure Service Fabric Mesh ausgeführten App'
description: In diesem Tutorial erfahren Sie, wie Sie eine in Service Fabric Mesh ausgeführte Service Fabric-Anwendung aktualisieren.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626560"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Tutorial: Aktualisieren einer in Service Fabric Mesh ausgeführten Service Fabric-Anwendung

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der dritte Teil einer Reihe. Hier erfahren Sie, wie Sie eine Service Fabric-Anwendung aktualisieren, die [zuvor in Service Fabric Mesh bereitgestellt wurde](service-fabric-mesh-tutorial-template-deploy-app.md), indem Sie die Anzahl der zugeordneten CPU-Ressourcen erhöhen.  Wenn Sie fertig sind, besitzen Sie einen Web-Front-End-Dienst, der mit einer höheren Anzahl von CPU-Ressourcen ausgeführt wird.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Ändern der Anwendungskonfigurationen
> * Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Bereitstellen einer Anwendung in Service Fabric Mesh mithilfe einer Vorlage](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skalieren einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-scale-services.md)
> * Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung
> * [Entfernen einer Anwendung](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Öffnen Sie [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md), oder [installieren Sie die Azure- und die Service Fabric Mesh-Befehlszeilenschnittstelle lokal](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Aktualisieren der Anwendungskonfigurationen

Einer der Hauptvorteile bei der Bereitstellung von Anwendungen in Service Fabric Mesh ist die Möglichkeit, Ihre Anwendungskonfiguration ganz einfach zu aktualisieren,  beispielsweise die CPU- oder Arbeitsspeicherressourcen für Ihre Dienste.

In diesem Tutorial wird die Beispiel-To-Do-Liste verwendet, die [zuvor bereitgestellt](service-fabric-mesh-tutorial-template-deploy-app.md) wurde und jetzt betriebsbereit sein sollte. Die Anwendung verfügt über zwei Dienste: WebFrontEnd und ToDoService. Jeder Dienst wurde ursprünglich mit einem Wert von 0,5 für CPU-Ressourcen bereitgestellt.  Führen Sie zum Anzeigen der CPU-Ressourcen für den WebFrontEnd-Dienst Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

In der Bereitstellungsvorlage für die Anwendungsressource weist jeder Dienst die Eigenschaft *cpu* auf, mit der Sie die angeforderten CPU-Ressourcen festlegen können. Eine Anwendung kann aus mehreren zusammen bereitgestellten und verwalteten Diensten bestehen, wobei jeder Dienst eine eindeutige *cpu*-Einstellung aufweist. Wenn Sie die Anzahl von CPU-Ressourcen des Web-Front-End-Diensts erhöhen möchten, ändern Sie in der Bereitstellungsvorlage oder in der Parameterdatei den Wert *cpu*.  Führen Sie anschließend ein Upgrade der Anwendung durch.

### <a name="modify-the-deployment-template-parameters"></a>Ändern der Bereitstellungsvorlagenparameter

Wenn Ihre Vorlage Werte enthält, die Sie nach Bereitstellen der Anwendung wahrscheinlich ändern werden, oder wenn Sie gerne die Möglichkeit hätten, die Werte pro Bereitstellung zu ändern (falls diese Vorlage für andere Bereitstellungen wiederverwendet werden soll), ist es empfehlenswert, die Werte zu parametrisieren.

Zuvor wurde die Anwendung mit der [Bereitstellungsvorlage „mesh_rp.windows.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) und der [Parameterdatei „mesh_rp.windows.parameter.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) bereitgestellt.

Öffnen Sie die [Parameterdatei „mesh_rp.windows.parameter.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) lokal, und legen Sie den Wert *frontEndCpu* auf 1 fest:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Speichern Sie Ihre Änderungen an der Parameterdatei.  

Der Parameter *frontEndCpu* wird im Abschnitt *parameters* der [Bereitstellungsvorlage „mesh_rp.windows.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) deklariert:

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

Die Eigenschaft *codePackages->resources->requests->cpu* des WebFrontEnd-Diensts verweist auf den Parameter *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Upgraden Ihrer Anwendung

Während die Anwendung ausgeführt wird, können Sie sie durch erneute Bereitstellung der Vorlage und der aktualisierten Parameterdatei aktualisieren:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Dadurch wird ein paralleles Upgrade für Ihre Anwendung gestartet, und die CPU-Ressourcen sollten in wenigen Minuten erhöht werden.  Führen Sie zum Anzeigen der CPU-Ressourcen für den WebFrontEnd-Dienst Folgendes aus:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Ändern der Anwendungskonfigurationen
> * Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Tutorial: Remove an application and resources](service-fabric-mesh-tutorial-template-remove-app.md) (Tutorial: Entfernen von Anwendungen und Ressourcen)
