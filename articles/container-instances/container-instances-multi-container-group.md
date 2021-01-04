---
title: 'Tutorial: Bereitstellen einer Gruppe mit mehreren Containern – Vorlage'
description: In diesem Tutorial erfahren Sie, wie Sie eine Containergruppe mit mehreren Containern über die Azure-Befehlszeilenschnittstelle mit einer Azure Resource Manager-Vorlage in Azure Container Instances bereitstellen.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bc956bed8324398c2d60f4641cd0bcb821fb51c2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091343"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Tutorial: Bereitstellen einer Gruppe mit mehreren Containern über eine Resource Manager-Vorlage

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Ressourcen-Manager](container-instances-multi-container-group.md)

Azure Container Instances unterstützt die Bereitstellung von mehreren Containern auf einem einzelnen Host mit einer [Containergruppe](container-instances-container-groups.md). Containergruppen sind nützlich, wenn eine Sidecaranwendung für die Protokollierung, die Überwachung oder eine andere Konfiguration, bei der ein Dienst einen zweiten angefügten Prozess benötigt, erstellt wird.

In diesem Tutorial befolgen Sie Schritte zum Ausführen einer einfachen Sidecarkonfiguration mit zwei Containern durch Bereitstellung einer Azure Resource Manager-Vorlage mithilfe der Azure-Befehlszeilenschnittstelle. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Konfigurieren einer Vorlage für eine Gruppe mit mehreren Containern
> * Bereitstellen der Containergruppe
> * Anzeigen der Protokolle der Container

Eine Resource Manager-Vorlage kann für Szenarien angepasst werden, in denen Sie mit der Containergruppe zusätzliche Azure-Dienstressourcen (z. B. eine Azure Files-Freigabe oder ein virtuelles Netzwerk) bereitstellen müssen. 

> [!NOTE]
> Gruppen mit mehreren Containern sind aktuell auf Linux-Container beschränkt. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Konfigurieren einer Vorlage

Beginnen Sie, indem Sie den folgenden JSON-Code in eine neue Datei mit dem Namen `azuredeploy.json` kopieren. In Azure Cloud Shell können Sie die Datei mit Visual Studio Code in Ihrem Arbeitsverzeichnis erstellen:

```
code azuredeploy.json
```

Diese Resource Manager-Vorlage definiert eine Containergruppe mit zwei Containern, einer öffentlichen IP-Adresse und zwei verfügbar gemachten Ports. Der erste Container in der Gruppe führt eine Webanwendung mit Internetverbindung aus. Der zweite Container, der Sidecar, stellt eine HTTP-Anforderung an die Hauptwebanwendung über das lokale Netzwerk der Gruppe.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Fügen Sie ein Objekt zum JSON-Dokument mit dem folgenden Format hinzu, um einen Container der privaten Imageregistrierung verwenden zu können. Eine Beispielimplementierung dieser Konfiguration finden Sie in der Dokumentation [Referenz zu ACI Resource Manager-Vorlagen][template-reference].

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Stellen Sie die Vorlage mit dem Befehl [az deployment group create][az-deployment-group-create] bereit.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

Innerhalb weniger Sekunden sollten Sie eine erste Antwort von Azure erhalten.

## <a name="view-deployment-state"></a>Zusammenfassung der Bereitstellungen anzeigen

Um den Bereitstellungsstatus anzuzeigen, verwenden Sie den folgenden Befehl [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Wenn Sie die laufende Anwendung anzeigen möchten, navigieren Sie zu ihrer IP-Adresse in Ihrem Browser. In dieser Beispielausgabe lautet die IP `52.168.26.124`:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Containerprotokolle anzeigen

Zeigen Sie die Protokollausgabe eines Containers mithilfe des Befehls [az container logs][az-container-logs] an. Das `--container-name`-Argument gibt den Container an, aus dem Protokolle erhalten werden können. In diesem Beispiel wird der Container `aci-tutorial-app` angegeben.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Ausgabe:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Führen Sie einen ähnlichen Befehl aus, in dem Sie den Container `aci-tutorial-sidecar` angeben, um die Protokolle für den Sidecarcontainer anzuzeigen.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Ausgabe:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Wie Sie sehen können, erfolgt vom Sidecar in regelmäßigen Abständen eine HTTP-Anforderung an die Hauptwebanwendung über das lokale Netzwerk der Gruppe, um sicherzustellen, dass er ausgeführt wird. Dieses Sidecarbeispiel kann erweitert werden, um eine Warnung auszulösen, wenn ein anderer HTTP-Antwortcode als `200 OK` empfangen wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Containergruppe mit mehreren Containern mit einer Azure Resource Manager-Vorlage in Azure Container Instances bereitgestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren einer Vorlage für eine Gruppe mit mehreren Containern
> * Bereitstellen der Containergruppe
> * Anzeigen der Protokolle der Container

Weitere Beispiele für Vorlagen finden Sie unter [Azure Resource Manager-Vorlagen für Azure Container Instances](container-instances-samples-rm.md).

Sie können eine Gruppe mit mehreren Containern auch mithilfe einer [YAML-Datei](container-instances-multi-container-yaml.md) angeben. Das YAML-Format ist präziser, daher ist die Bereitstellung mit einer YAML-Datei eine gute Wahl, wenn Ihre Bereitstellung nur Containerinstanzen enthält.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
