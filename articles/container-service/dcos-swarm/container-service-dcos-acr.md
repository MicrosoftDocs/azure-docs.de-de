---
title: (VERALTET) Verwenden von ACR mit einem Azure DC/OS-Cluster
description: Verwenden von Azure Container Registry mit einem DC/OS-Cluster in Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 8319f2f5405271679d0c11d4ac68492cdec8fc14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66148930"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(VERALTET) Verwenden von ACR mit einem DC/OS-Cluster zum Bereitstellen Ihrer Anwendung

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In diesem Artikel wird erläutert, wie Sie Azure Container Registry (ACR) mit einem DC/OS-Cluster verwenden. Mithilfe von ACR können Sie Containerimages privat speichern und verwalten. Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Bereitstellen von Azure Container Registry (sofern erforderlich)
> * Konfigurieren der ACR-Authentifizierung in einem DC/OS-Cluster
> * Hochladen eines Images in Azure Container Registry
> * Ausführen eines Containerimages aus Azure Container Registry

Zum Ausführen der Schritte in diesem Tutorial benötigen Sie einen ACS-DC/OS-Cluster. Bei Bedarf können Sie mit diesem [Beispielskript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) einen erstellen.

Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle]( /cli/azure/install-azure-cli) weitere Informationen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Bereitstellen von Azure Container Registry

Erstellen Sie bei Bedarf mit dem Befehl [az acr create](/cli/azure/acr#az-acr-create) eine Azure Container Registry-Instanz. 

Im folgenden Beispiel wird eine Registrierung mit einem zufällig generierten Namen erstellt. Die Registrierung wird zudem mit dem Argument `--admin-enabled` mit einem Administratorkonto konfiguriert.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Nach der Erstellung der Registrierung gibt die Azure-Befehlszeilenschnittstelle Daten ähnlich den folgenden aus. Notieren Sie die Werte für `name` und `loginServer`, diese werden in späteren Schritten verwendet.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Rufen Sie die Anmeldeinformationen der Containerregistrierung mit dem Befehl [az acr credential show](/cli/azure/acr/credential) ab. Ersetzen Sie den Wert für `--name` durch den im vorherigen Schritt notierten Wert. Notieren Sie ein Kennwort, Sie benötigen es in einem späteren Schritt.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Weitere Informationen zu Azure Container Registry finden Sie unter [Einführung in private Docker-Containerregistrierungen](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Verwalten der ACR-Authentifizierung

Bei der herkömmlichen Art, Images per Push und Pull in einer privaten Registrierung zu verwalten, muss zunächst eine Authentifizierung für die Registrierung erfolgen. Dazu können Sie den Befehl `docker login` auf jedem Client verwenden, der Zugriff auf die private Registrierung benötigt. Da ein DC/OS-Cluster viele Knoten enthalten kann, die alle mit ACR authentifiziert werden müssen, empfiehlt es sich, diesen Prozess für alle Knoten zu automatisieren. 

### <a name="create-shared-storage"></a>Erstellen eines freigegebenen Speichers

Bei diesem Prozess wird eine Azure-Dateifreigabe verwendet, die auf jedem Knoten im Cluster eingebunden wurde. Wenn Sie noch keinen freigegebenen Speicher eingerichtet haben, finden Sie entsprechende Informationen unter [Einrichten einer Dateifreigabe in einem DC/OS-Cluster](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Konfigurieren der ACR-Authentifizierung

Rufen Sie zunächst den vollqualifizierten Domänennamen (FQDN) des DC/OS-Masters ab, und speichern Sie ihn in einer Variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Erstellen Sie eine SSH-Verbindung mit dem Master (oder dem ersten Master) des DC/OS-basierten Clusters. Aktualisieren Sie den Benutzernamen, wenn ein nicht standardmäßiger Wert bei der Erstellung des Clusters verwendet wurde.

```azurecli-interactive
ssh azureuser@$FQDN
```

Führen Sie den folgenden Befehl für die Anmeldung bei Azure Container Registry aus. Ersetzen Sie `--username` durch den Namen der Containerregistrierung und `--password` durch eines der bereitgestellten Kennwörter. Ersetzen Sie das letzte Argument *mycontainerregistry.azurecr.io* im Beispiel durch den loginServer-Namen der Containerregistrierung. 

Mit diesem Befehl wird der Authentifizierungswert lokal unter dem Pfad `~/.docker` gespeichert.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Erstellen Sie eine komprimierte Datei, die die Authentifizierungswerte der Containerregistrierung enthält.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Kopieren Sie diese Datei in den freigegebenen Speicher des Clusters. Durch diesen Schritt wird die Datei auf allen Knoten des DC/OS-Clusters verfügbar.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Hochladen eines Images in ACR

Erstellen Sie über einen Entwicklungscomputer oder ein anderes System, auf dem Docker installiert ist, ein Image, und laden Sie es in Azure Container Registry hoch.

Erstellen Sie einen Container aus dem Ubuntu-Image.

```azurecli-interactive
docker run ubuntu --name base-image
```

Erfassen Sie dann den Container in einem neuen Image. Der Imagename muss den `loginServer`-Namen der Containerregistrierung mit dem Format `loginServer/imageName` enthalten.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Melden Sie sich bei Azure Container Registry an. Ersetzen Sie den Namen durch den loginServer-Namen, „--username“ durch den Namen der Containerregistrierung und „--password“ durch eines der bereitgestellten Kennwörter.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Laden Sie schließlich das Image in die ACR-Registrierung hoch. In diesem Beispiel wird ein Image mit dem Namen „dcos-demo“ hochgeladen.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Ausführen eines Images aus ACR

Um ein Image aus der ACR-Registrierung zu verwenden, erstellen Sie die Datei *acrDemo.json*, und kopieren Sie den folgenden Text in diese Datei. Ersetzen Sie den Imagenamen durch den loginServer-Namen und Imagenamen der Containerregistrierung, z.B. `loginServer/imageName`. Notieren Sie den Wert der `uris`-Eigenschaft. Diese Eigenschaft enthält den Speicherort der Authentifizierungsdatei für die Containerregistrierung, bei der es sich in diesem Fall um die Azure-Dateifreigabe handelt, die auf jedem Knoten im DC/OS-Cluster eingebunden ist.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Stellen Sie die Anwendung über die DC/OS-Befehlszeilenschnittstelle bereit.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie DC/OS für die Verwendung von Azure Container Registry konfiguriert. Dazu haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Bereitstellen von Azure Container Registry (sofern erforderlich)
> * Konfigurieren der ACR-Authentifizierung in einem DC/OS-Cluster
> * Hochladen eines Images in Azure Container Registry
> * Ausführen eines Containerimages aus Azure Container Registry
