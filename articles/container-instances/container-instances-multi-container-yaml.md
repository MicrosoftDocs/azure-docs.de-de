---
title: 'Tutorial: Bereitstellen einer Gruppe mit mehreren Containern – YAML'
description: In diesem Tutorial erfahren Sie, wie Sie eine Containergruppe mit mehreren Containern über die Azure-Befehlszeilenschnittstelle mit einer YAML-Datei in Azure Container Instances bereitstellen.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 8f1f13d4fa7e32b76988cdf356d5d1bb0528f824
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091292"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Bereitstellen einer Gruppe mit mehreren Containern mithilfe einer YAML-Datei

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Ressourcen-Manager](container-instances-multi-container-group.md)
>

Azure Container Instances unterstützt die Bereitstellung von mehreren Containern auf einem einzelnen Host mit einer [Containergruppe](container-instances-container-groups.md). Containergruppen sind nützlich, wenn eine Sidecaranwendung für die Protokollierung, die Überwachung oder eine andere Konfiguration, bei der ein Dienst einen zweiten angefügten Prozess benötigt, erstellt wird.

In diesem Tutorial befolgen Sie Schritte zum Ausführen einer einfachen Sidecarkonfiguration mit zwei Containern durch Bereitstellung einer [YAML-Datei](container-instances-reference-yaml.md) mithilfe der Azure-Befehlszeilenschnittstelle. Eine YAML-Datei bietet ein übersichtliches Format zur Angabe der Instanzeinstellungen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Konfigurieren einer YAML-Datei
> * Bereitstellen der Containergruppe
> * Anzeigen der Protokolle der Container

> [!NOTE]
> Gruppen mit mehreren Containern sind aktuell auf Linux-Container beschränkt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Konfigurieren einer YAML-Datei

Um eine Gruppe mit mehreren Containern mit dem Befehl [az container create][az-container-create] in der Azure-Befehlszeilenschnittstelle bereitzustellen, müssen Sie die Containergruppenkonfiguration in einer YAML-Datei angeben. Übergeben Sie die YAML-Datei dann als Parameter an den Befehl.

Starten Sie, indem Sie den folgenden YAML-Inhalt in eine neue Datei mit dem Namen **deploy-aci.yaml** kopieren. In Azure Cloud Shell können Sie die Datei mit Visual Studio Code in Ihrem Arbeitsverzeichnis erstellen:

```
code deploy-aci.yaml
```

Diese YAML-Datei definiert eine Containergruppe namens „myContainerGroup“ mit zwei Containern, einer öffentlichen IP-Adresse und zwei verfügbar gemachten Ports. Die Container werden aus öffentlichen Microsoft-Images bereitgestellt. Der erste Container in der Gruppe führt eine Webanwendung mit Internetverbindung aus. Vom zweiten Container, dem Sidecar, erfolgen in regelmäßigen Abständen HTTP-Anforderungen an die Webanwendung, die im ersten Container über das lokale Netzwerk der Containergruppe ausgeführt wird.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Um eine private Containerimageregistrierung zu verwenden, fügen Sie die `imageRegistryCredentials`-Eigenschaft mit für Ihre Umgebung entsprechend geänderten Werten in der Containergruppe hinzu:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Bereitstellen der Containergruppe

Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Stellen Sie die Containergruppe mit dem Befehl [az container create][az-container-create] bereit, und übergeben Sie dabei die YAML-Datei als Argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

In diesem Tutorial haben Sie eine Gruppe mit mehreren Containern mit einer YAML-Datei in Azure Container Instances bereitgestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren einer YAML-Datei für eine Gruppe mit mehreren Containern
> * Bereitstellen der Containergruppe
> * Anzeigen der Protokolle der Container

Sie können eine Gruppe mit mehreren Containern auch mithilfe einer [Resource Manager-Vorlage](container-instances-multi-container-group.md) angeben. Eine Resource Manager-Vorlage kann für Szenarien angepasst werden, in denen Sie mit der Containergruppe zusätzliche Azure-Dienstressourcen bereitstellen möchten.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
