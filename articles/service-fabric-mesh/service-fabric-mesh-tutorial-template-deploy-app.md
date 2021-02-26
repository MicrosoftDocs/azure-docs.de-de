---
title: 'Tutorial: Bereitstellen einer App in Azure Service Fabric Mesh'
description: In diesem Tutorial wird beschrieben, wie Sie mithilfe einer Vorlage eine Anwendung in Service Fabric Mesh bereitstellen.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 589e881eb48daf7da9cd2a934b14acfcc76dc5f9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625415"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Tutorial: Bereitstellen einer Anwendung in Service Fabric Mesh mithilfe einer Vorlage

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der erste Teil einer Serie. Hier lernen Sie, wie Sie mithilfe einer Vorlage eine Azure Service Fabric Mesh-Anwendung bereitstellen.  Die Anwendung setzt sich aus einem ASP.NET-Web-Front-End-Dienst und einem ASP.NET Core-Web-API-Back-End-Dienst zusammen, die in Docker Hub enthalten sind.  Sie rufen die beiden Containerimages aus Docker Hub ab und übertragen sie per Push an Ihre eigene private Registrierung. Anschließend erstellen Sie eine Azure RM-Vorlage für die Anwendung und stellen die Anwendung aus Ihrer Containerregistrierung in Service Fabric Mesh bereit. Wenn Sie fertig sind, besitzen Sie eine einfache Aufgabenlistenanwendung, die in Service Fabric Mesh ausgeführt wird.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer privaten Azure Container Registry-Instanz
> * Übertragen eines Containerimages an die Registrierung per Push
> * Erstellen von RM-Vorlage und Parameterdateien
> * Bereitstellen einer Anwendung in Service Fabric Mesh

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Bereitstellen einer Anwendung in Service Fabric Mesh mithilfe einer Vorlage
> * [Skalieren von Diensten in einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Aktualisieren einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Entfernen einer Anwendung](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* [Installieren von Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Installieren Sie die Azure- und die Azure Service Fabric Mesh-Befehlszeilenschnittstelle lokal.](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Die mit den Diensten in Ihrer Service Fabric Mesh-Anwendung verknüpften Containerimages müssen in einer Containerregistrierung gespeichert werden.  In diesem Tutorial wird eine private Instanz von Azure Container Registry (ACR) verwendet. 

Führen Sie die folgenden Schritte aus, um eine ACR-Instanz zu erstellen.  Wenn Sie bereits eine ACR-Instanz eingerichtet haben, können Sie diese Schritte überspringen.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich bei Azure an, und legen Sie das aktive Abonnement fest.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe namens *myResourceGroup* am Standort *eastus* zu erstellen.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Erstellen der Containerregistrierung

Erstellen Sie mithilfe des Befehls `az acr create` eine ACR-Instanz. Der Registrierungsname muss innerhalb von Azure eindeutig sein und aus 5 bis 50 alphanumerischen Zeichen bestehen. Im folgenden Beispiel wird der Name *myContainerRegistry* verwendet. Wenn Sie eine Fehlermeldung mit dem Hinweis erhalten, dass der Name bereits verwendet wird, wählen Sie einen anderen Namen aus.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Wenn die Registrierung erstellt wird, sieht die Ausgabe etwa wie folgt aus:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Übertragen der Images an Azure Container Registry per Push

In diesem Tutorial wird die Aufgabenlistenanwendung als Beispiel verwendet.  Die Containerimages für die Dienste [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) und [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) befinden sich in Docker Hub. Informationen zum Erstellen der Anwendung in Visual Studio finden Sie unter [Tutorial: Erstellen, Debuggen, Bereitstellen und Aktualisieren einer Service Fabric Mesh-App mit mehreren Diensten](service-fabric-mesh-tutorial-create-dotnetcore.md). In Service Fabric Mesh können Windows-oder Linux-Docker-Container ausgeführt werden.  Wählen Sie bei der Verwendung von Linux-Containern in Docker die Option **Switch to Linux containers** (Zu Linux-Containern wechseln) aus.  Wählen Sie bei der Verwendung von Windows-Containern in Docker die Option **Switch to Windows containers** (Zu Windows-Containern wechseln) aus.

Um ein Image per Push an eine ACR-Instanz übertragen zu können, benötigen Sie zunächst ein Containerimage. Falls Sie noch keine lokalen Containerimages besitzen, rufen Sie mithilfe des Befehls [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) die Images [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) und [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) per Pull aus Docker Hub ab.

>[!NOTE]
> Ab dem 2. November 2020 [gelten die Grenzwerte für die Downloadrate](https://docs.docker.com/docker-hub/download-rate-limit/) für anonyme und authentifizierte Anforderungen an Docker Hub von Docker-Konten im Plan „Free“. Diese Grenzwerte werden durch die IP-Adresse erzwungen. 
> 
> Diese Befehle nutzen öffentliche Images aus Docker Hub. Beachten Sie, dass möglicherweise Ratenbeschränkungen gelten. Ausführlichere Informationen finden Sie unter [Authentifizieren mit Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Abrufen der Windows-Images:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Bevor Sie ein Image mithilfe von Push in Ihre Registrierung übertragen können, müssen Sie es mit dem vollqualifizierten Namen Ihres ACR-Anmeldeservers markieren.

Führen Sie den folgenden Befehl aus, um den vollständigen Anmeldeservernamen Ihrer ACR-Instanz abzurufen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Versehen Sie nun das Docker-Image mithilfe des Befehls [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) mit einem Tag. Im folgenden Beispiel werden die Images „seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709“ und „seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709“ mit einem Tag versehen. Wenn Sie verschiedene Images verwenden, ersetzen Sie im folgenden Befehl diese Imagenamen:

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Melden Sie sich bei Azure Container Registry an.

```azurecli
az acr login -n myContainerRegistry
```

Übertragen Sie das Image mit dem Befehl [docker push](https://docs.docker.com/engine/reference/commandline/push/) per Push an die ACR-Instanz:

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Auflisten von Containerimages

Führen Sie den folgenden Befehl aus, um die Repositorys in Ihrer ACR-Instanz anzuzeigen:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

Das folgende Beispiel listet die Tags des Repositorys **azure-mesh-todo-service** auf:

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

Die vorhergehende Ausgabe bestätigt das Vorhandensein von `azure-mesh-todo-service:1.0-nanoserver-1709` in der privaten Containerregistrierung.  Überprüfen Sie auch, ob `azure-mesh-todo-webfrontend:1.0-nanoserver-1709` vorhanden ist.

## <a name="retrieve-credentials-for-the-registry"></a>Abrufen von Anmeldeinformationen für die Registrierung

> [!IMPORTANT]
> Die Aktivierung des Administratorbenutzers in einer ACR-Instanz wird für Produktionsszenarien nicht empfohlen. Der Einfachheit halber wird er hier aktiviert. Verwenden Sie in Produktionsszenarien sowohl für die Benutzer- als auch die Systemauthentifizierung einen [Dienstprinzipal](../container-registry/container-registry-auth-service-principal.md).

Um eine Containerinstanz aus der Registrierung bereitzustellen, die mithilfe einer Vorlage erstellt wurde, müssen Sie bei der Bereitstellung die Registrierungsanmeldeinformationen angeben. Aktivieren Sie zunächst den Administratorbenutzer in Ihrer Registrierung mit dem folgenden Befehl:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Rufen Sie anschließend mit den folgenden Befehlen den Namen, den Benutzernamen und das Kennwort des Anmeldeservers für die Registrierung ab:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Verwenden Sie die zurückgegebenen Werte für Name, Benutzername und Kennwort des ACR-Anmeldeservers, wenn Sie im nächsten Abschnitt die RM-Vorlage und die Parameterdateien erstellen.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Herunterladen und Erkunden der Vorlage und der Parameterdateien

Eine Service Fabric Mesh-Anwendung ist eine Azure-Ressource, die Sie mithilfe von Azure Resource Manager-Vorlagen (RM) bereitstellen und verwalten können. Wenn Sie mit den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen nicht vertraut sind, lesen Sie die Informationen unter [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md).

In diesem Tutorial wird die Aufgabenlistenanwendung als Beispiel verwendet.  Laden Sie die Dateien [mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) (Bereitstellungsvorlage) und [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) (Parameterdatei) herunter, statt eine neue Vorlage und neue Parameterdateien zu erstellen.

### <a name="parameters"></a>Parameter
Wenn Ihre Vorlage Werte enthält, die Sie nach Bereitstellen der Anwendung wahrscheinlich ändern werden, oder wenn Sie gerne die Möglichkeit hätten, die Werte pro Bereitstellung zu ändern (falls diese Vorlage für andere Bereitstellungen wiederverwendet werden soll), ist es empfehlenswert, die Werte zu parametrisieren. Zu diesem Zweck muss am Anfang der Bereitstellungsvorlage ein Abschnitt namens „parameters“ erstellt werden. Dieser Abschnitt dient zum Angeben von Parameternamen und Eigenschaften, auf die später in der Bereitstellungsvorlage verwiesen wird. Jede Parameterdefinition enthält die Einträge *type*, *defaultValue* und einen optionalen *metadata*-Abschnitt mit einer Beschreibung (*description*).

Der Abschnitt „parameters“ wird am Anfang Ihrer Bereitstellungsvorlage direkt vor dem Abschnitt *resources* definiert:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

In der [Bereitstellungsvorlage „mesh_rp.windows.json“](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) werden die folgenden Parameter deklariert: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Beschreibungen für die einzelnen Parameter finden Sie in der Bereitstellungsvorlagendatei.

Diese Parameter werden in der Parameterdatei [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) verwendet.  Wenn Sie eine separate Parameterdatei verwenden, haben Sie die Möglichkeit, Parameterwerte von Bereitstellung zu Bereitstellung zu ändern, ohne die Bereitstellungsvorlage selbst ändern zu müssen.

### <a name="overview-of-the-application-and-services"></a>Übersicht über die Anwendung und Dienste

Dienste werden in der Vorlage als Eigenschaften der Anwendungsressource angegeben.  Anwendungen werden in einem privaten Netzwerk bereitgestellt, das als Ressource in der Vorlage deklariert wird.  Dienste können Volumes zum dauerhaften Speichern von Daten verwenden, die als Ressourcen in der Vorlage deklariert werden.  Für jeden Dienst werden der Betriebssystemtyp, Codepakete, die Anzahl von Replikaten und das Netzwerk als Eigenschaften des Diensts angegeben.  Geben Sie für jedes Codepaket das Containerimage, Endpunkte, Arbeitsspeicher- und CPU-Ressourcen und Imagerepository-Anmeldeinformationen an. Ganz allgemein sieht die Vorlage für eine Service Fabric Mesh-Anwendung mit mehreren Diensten wie folgt aus:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

In der Bereitstellungsvorlagendatei [mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) finden Sie Details zur Aufgabenlistenanwendung.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Bereitstellen der Anwendung in Service Fabric Mesh
Erstellen Sie die Anwendung und die zugehörigen Ressourcen mit dem folgenden Befehl, und geben Sie die Anmeldeinformationen aus dem vorherigen Schritt [Abrufen von Anmeldeinformationen für die Registrierung](#retrieve-credentials-for-the-registry) ein.

Aktualisieren Sie in der Parameterdatei die folgenden Parameterwerte:

|Parameter|Wert|
|---|---|
|location|Die Region für die Bereitstellung der Anwendung.  Beispiel: eastus|
|registryPassword|Das Kennwort, das Sie zuvor unter [Abrufen von Anmeldeinformationen für die Registrierung](#retrieve-credentials-for-the-registry) abgerufen haben. Dieser Parameter in der Vorlage ist eine sichere Zeichenfolge und wird nicht im Bereitstellungsstatus oder in den `az mesh service show`-Befehlen angezeigt.|
|registryUserName|Der Benutzername, den Sie unter [Abrufen von Anmeldeinformationen für die Registrierung](#retrieve-credentials-for-the-registry) abgerufen haben.|
|registryServer|Der Registrierungsservername, den Sie unter [Abrufen von Anmeldeinformationen für die Registrierung](#retrieve-credentials-for-the-registry) abgerufen haben.|
|frontEndImage|Das Containerimage für den Front-End-Dienst.  Beispiel: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|Das Containerimage für den Back-End-Dienst.  Beispiel: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Führen Sie zum Bereitstellen der Anwendung Folgendes aus:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Mit diesem Befehl wird der unten gezeigte JSON-Codeausschnitt erstellt. Kopieren Sie im Abschnitt ```outputs``` der JSON-Ausgabe die Eigenschaft ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Diese Informationen stammen aus dem Abschnitt ```outputs``` in der ARM-Vorlage. Dieser Abschnitt verweist auf die Gatewayressource, um die öffentliche IP-Adresse abzurufen: 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Öffnen der Anwendung

Nachdem die Anwendung erfolgreich bereitgestellt wurde, rufen Sie die öffentliche IP-Adresse für den Dienstendpunkt ab. Der Bereitstellungsbefehl gibt die öffentliche IP-Adresse des Dienstendpunkts zurück. Optional können Sie auch die Netzwerkressource abfragen, um die öffentliche IP-Adresse des Dienstendpunkts zu finden. Der Netzwerkressourcenname für diese Anwendung ist `todolistappNetwork`. Informationen dazu können Sie mithilfe des folgenden Befehls abrufen. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Navigieren Sie in einem Webbrowser zur IP-Adresse.

## <a name="check-application-status"></a>Überprüfen des Anwendungsstatus

Sie können den Status der Anwendung mit dem Befehl „app show“ überprüfen. Der Anwendungsname für die bereitgestellte Anwendung ist „todolistapp“. Sie können deren Details abrufen.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Untersuchen Sie die Protokolle für die bereitgestellte Anwendung mit dem Befehl `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer privaten Containerregistrierung
> * Übertragen eines Containerimages an die Registrierung per Push
> * Erstellen von Vorlage und Parameterdateien
> * Bereitstellen einer Anwendung in Service Fabric Mesh

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Skalieren einer in Service Fabric Mesh ausgeführten Anwendung](service-fabric-mesh-tutorial-template-scale-services.md)