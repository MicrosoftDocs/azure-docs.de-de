---
title: Verwenden eines auf Azure Files basierenden Volumes in einer Service Fabric Mesh-App
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle und durch Einbinden eines auf Azure Files basierenden Volumes in einem Dienst den Status in der Azure Service Fabric Mesh-Anwendung speichern.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 00addbe992bc113c67df08f73414107d51b3bce5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007391"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Einbinden eines auf Azure Files basierenden Volumes in einer Service Fabric Mesh-Anwendung 

In diesem Artikel wird das Einbinden eines auf Azure Files basierenden Volumes in einen Dienst einer Service Fabric Mesh-Anwendung beschrieben.  Bei dem Azure Files-Volumetreiber handelt es sich um einen Docker-Volumetreiber, der verwendet wird, um eine Azure Files-Freigabe in einen Container einzubinden, der zum Speichern eines Dienststatus verwendet wird. Volumes bieten Ihnen einen universellen Dateispeicher und ermöglichen das Lesen und Schreiben von Dateien über normale Datenträger-E/A-Datei-APIs.  Weitere Informationen zu Volumes und Optionen für das Speichern von Anwendungsdaten finden Sie unter [Zustandsspeicherung](service-fabric-mesh-storing-state.md).

Erstellen Sie eine Volumeressource in Ihrer Service Fabric Mesh-Anwendung, und verweisen Sie in Ihrem Dienst auf dieses Volume, um ein Volume in einen Dienst einzubinden.  Das Deklarieren der Volumeressource und das Verweisen auf dieselbe in der Dienstressource kann über [YAML-basierte Ressourcendateien](#declare-a-volume-resource-and-update-the-service-resource-yaml) oder über die [JSON-basierte Bereitstellungsvorlage](#declare-a-volume-resource-and-update-the-service-resource-json) erfolgen. Bevor Sie das Volume einbinden, müssen Sie zunächst ein Azure-Speicherkonto und eine [Dateifreigabe in Azure Files](../storage/files/storage-how-to-create-file-share.md) erstellen.

## <a name="prerequisites"></a>Voraussetzungen
> [!NOTE]
> **Bekanntes Problem bei der Bereitstellung auf dem Windows RS5-Entwicklungscomputer:** Ein offener Bug von PowerShell-Cmdlet New-SmbGlobalMapping auf Windows RS5-Computern verhindert die Einbindung von Azure Files-Volumes. Der folgende Beispielfehler tritt auf, wenn ein Azure Files-basiertes Volume auf einem lokalen Entwicklungscomputer eingebunden wird.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Zur Problemumgehung muss zuerst der folgende Befehl mit PowerShell-Administratorrechten ausgeführt und dann der Computer neu gestartet werden.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Sie können Azure Cloud Shell oder eine lokale Installation der Azure CLI für diesen Artikel verwenden. 

Wenn Sie die Azure CLI für diesen Artikel lokal verwenden möchten, muss `az --version` mindestens `azure-cli (2.0.43)` zurückgeben.  Installieren (oder aktualisieren) Sie das CLI-Erweiterungsmodul von Azure Service Fabric Mesh, indem Sie [diese Anweisungen](service-fabric-mesh-howto-setup-cli.md) befolgen.

Melden Sie sich folgendermaßen in Azure an, und legen Sie Ihr Abonnement fest:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Erstellen eines Speicherkontos und einer Dateifreigabe (optional)
Für das Einbinden eines Azure Files-Volumes sind ein Speicherkonto und eine Dateifreigabe erforderlich.  Sie können ein vorhandenes Azure-Speicherkonto und eine vorhandene Dateifreigabe verwenden oder die entsprechenden Ressourcen erstellen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Abrufen des Speicherkontonamens und -schlüssels und des Dateifreigabenamens
Der Speicherkontoname, der Speicherkontoschlüssel und der Dateifreigabename werden in den folgenden Abschnitten als `<storageAccountName>`, `<storageAccountKey>` und `<fileShareName>` bezeichnet. 

Listen Sie Ihre Speicherkonten auf, und rufen Sie den Namen des Speicherkontos mit der Dateifreigabe ab, das Sie verwenden möchten:
```azurecli-interactive
az storage account list
```

Rufen Sie den Dateifreigabenamen ab:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Rufen Sie den Speicherkontoschlüssel („key1“) ab:
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Sie können diese Werte auch im [Azure-Portal](https://portal.azure.com) abrufen:
* `<storageAccountName>`: Unter **Speicherkonten** ist der Name des Speicherkontos angegeben, das Sie zum Erstellen der Dateifreigabe verwendet haben.
* `<storageAccountKey>` Wählen Sie unter **Speicherkonten** Ihr Speicherkonto aus, klicken Sie auf **Zugriffsschlüssel**, und verwenden Sie den Wert unter **key1**.
* `<fileShareName>` Wählen Sie unter **Speicherkonten** Ihr Speicherkonto aus, und klicken Sie dann auf **Dateien**. Als Name muss der Name der Dateifreigabe verwendet werden, die Sie erstellt haben.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarieren einer Volumeressource und Aktualisieren der Dienstressource (JSON)

Fügen Sie die Parameter für die Werte `<fileShareName>`, `<storageAccountName>` und `<storageAccountKey>` hinzu, die Sie in einem vorherigen Schritt ermittelt haben. 

Erstellen Sie eine Volumeressource als Peer der Anwendungsressource. Geben Sie einen Namen und den Anbieter an („SFAzureFile“, um das auf Azure Files basierende Volume zu verwenden). Geben Sie in `azureFileParameters` die Parameter für die Werte `<fileShareName>`, `<storageAccountName>` und `<storageAccountKey>` an, die Sie in einem vorherigen Schritt ermittelt haben.

Fügen Sie `volumeRefs` zum `codePackages`-Element des Diensts hinzu, um das Volume in den Dienst einzubinden.  `name` stellt die Ressourcen-ID für das Volume (oder einen Parameter für die Bereitstellungsvorlage der Volumeressource) und den Namen des Volumes dar, der in der Ressourcendatei „volume.yaml“ deklariert wurde.  Bei `destinationPath` handelt es sich um das lokale Verzeichnis, in das das Volume eingebunden wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarieren einer Volumeressource und Aktualisieren der Dienstressource (YAML)

Fügen Sie eine neue *volume.yaml*-Datei zum Verzeichnis *App-Ressourcen* Ihrer Anwendung hinzu.  Geben Sie einen Namen und den Anbieter an („SFAzureFile“, um das auf Azure Files basierende Volume zu verwenden). Bei `<fileShareName>`, `<storageAccountName>` und `<storageAccountKey>` handelt es sich um die Werte, die Sie in einem vorherigen Schritt ermittelt haben.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Aktualisieren Sie die Datei *service.yaml* im Verzeichnis *Service Resources* (Dienstressourcen), um das Volume in Ihren Dienst einzubinden.  Fügen Sie das `volumeRefs`-Element zum `codePackages`-Element hinzu.  `name` stellt die Ressourcen-ID für das Volume (oder einen Parameter für die Bereitstellungsvorlage der Volumeressource) und den Namen des Volumes dar, der in der Ressourcendatei „volume.yaml“ deklariert wurde.  Bei `destinationPath` handelt es sich um das lokale Verzeichnis, in das das Volume eingebunden wird.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Nächste Schritte

- Schauen Sie sich die Azure Files-Volume-Beispielanwendung in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) an.
- Weitere Informationen zum Service Fabric-Ressourcenmodell finden Sie unter [Service Fabric Mesh-Ressourcenmodell](service-fabric-mesh-service-fabric-resources.md).
- In der [Übersicht über Azure Service Fabric Mesh](service-fabric-mesh-overview.md) erfahren Sie mehr über Service Fabric Mesh.
