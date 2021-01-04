---
title: 'Azure Service Fabric: Anwendungsressourcenmodell'
description: Dieser Artikel bietet eine Übersicht über die Verwaltung einer Azure Service Fabric-Anwendung mit Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: f4fbd775ab479437c350dc24b9b5511f7a614c8b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576093"
---
# <a name="service-fabric-application-resource-model"></a>Service Fabric: Anwendungsressourcenmodell

Sie haben für die Bereitstellung von Azure Service Fabric-Anwendungen in Ihrem Service Fabric-Cluster mehrere Optionen. Es wird empfohlen, Azure Resource Manager zu verwenden. Bei Verwenden von Resource Manager können Anwendungen und Dienste in JSON beschrieben und anschließend in der gleichen Resource Manager-Vorlage wie der Cluster bereitgestellt werden. Im Unterschied zur Verwendung von PowerShell oder Azure CLI zur Bereitstellung und Verwaltung von Anwendungen müssen Sie bei Einsatz von Resource Manager nicht darauf warten, dass der Cluster bereit ist. Die Registrierung und Bereitstellung von Anwendungen kann in einem Schritt erfolgen. Resource Manager ist die beste Möglichkeit, den Anwendungslebenszyklus in Ihrem Cluster zu verwalten. Weitere Informationen finden Sie unter [Bewährte Methoden: Infrastructure-as-Code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Das Verwalten Ihrer Anwendungen als Ressourcen in Resource Manager kann Ihnen helfen, Verbesserungen in diesen Bereichen zu erzielen:

* Überwachungsprotokoll: Resource Manager überwacht jeden Vorgang und führt ein detailliertes Aktivitätsprotokoll. Ein Aktivitätsprotokoll kann Ihnen helfen, alle Änderungen an den Anwendungen und Ihrem Cluster nachzuverfolgen.
* Rollenbasierte Zugriffssteuerung: Sie können den Zugriff auf Cluster und Anwendungen, die im Cluster bereitgestellt werden, mit derselben Resource Manager-Vorlage verwalten.
* Effizienz der Verwaltung: Dank Resource Manager haben Sie eine Zentrale (das Azure-Portal) für die Verwaltung Ihres Clusters und Ihrer kritischen Anwendungsbereitstellungen.

In diesem Dokument erfahren Sie Folgendes:

> [!div class="checklist"]
>
> * Bereitstellen von Anwendungsressourcen mithilfe von Resource Manager
> * Aktualisieren von Anwendungsressourcen mithilfe von Resource Manager
> * Löschen von Anwendungsressourcen

## <a name="deploy-application-resources"></a>Bereitstellen von Anwendungsressourcen

Die allgemeinen Schritte, um eine Anwendung und ihre Dienste mithilfe des Anwendungsressourcenmodells von Resource Manager bereitzustellen, sind wie folgt:
1. Packen des Anwendungscodes
1. Hochladen des Pakets
1. Verweisen auf den Speicherort des Pakets in einer Resource Manager-Vorlage als Anwendungsressource 

Weitere Informationen finden Sie unter [Packen einer Anwendung](service-fabric-package-apps.md#create-an-sfpkg).

Erstellen Sie dann eine Resource Manager-Vorlage, aktualisieren Sie die Parameterdatei mit Anwendungsdetails, und stellen Sie die Vorlage im Service Fabric-Cluster bereit. [Erkunden Sie Beispiele](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Um eine Anwendung anhand einer Resource Manager-Vorlage bereitzustellen, müssen Sie über ein Speicherkonto verfügen. Das Speicherkonto wird für das Staging des Anwendungsimages verwendet. 

Sie können ein vorhandenes Speicherkonto wiederverwenden oder für das Staging Ihrer Anwendungen ein neues Speicherkonto erstellen. Wenn Sie ein vorhandenes Speicherkonto verwenden, können Sie diesen Schritt überspringen. 

![Speicherkonto erstellen][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurieren des Speicherkontos

Nach dem Erstellen des Speicherkontos müssen Sie einen Blobcontainer erstellen, in dem das Staging der Anwendungen erfolgen kann. Wechseln Sie im Azure-Portal zum Azure Storage-Konto, in dem Sie Ihre Anwendungen speichern möchten. Wählen Sie **Blobs** > **Container hinzufügen** aus. 

Ressourcen in Ihrem Cluster können geschützt werden, indem die öffentliche Zugriffsebene auf **privat** festgelegt wird. Sie können auf verschiedene Arten Zugriff gewähren:

* Autorisieren des Zugriffs auf Blobs und Warteschlangen mit [Azure Active Directory](../storage/common/storage-auth-aad-app.md)
* Gewähren von Zugriff auf Azure-Blobdaten und -Warteschlangendaten mithilfe von [Azure RBAC im Azure-Portal](../storage/common/storage-auth-aad-rbac-portal.md)
* Delegieren des Zugriffs mithilfe einer [Shared Access Signature](/rest/api/storageservices/delegate-access-with-shared-access-signature)

Das Beispiel im folgenden Screenshot verwendet den anonymen Lesezugriff für Blobs.

![Erstellen des Blobs][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Staging der Anwendung in einem Speicherkonto

Bevor Sie eine Anwendung bereitstellen können, müssen Sie die Anwendung in Blob Storage stagen. In diesem Tutorial wird das Anwendungspaket manuell erstellt. Beachten Sie, dass dieser Schritt automatisiert werden kann. Weitere Informationen finden Sie unter [Packen einer Anwendung](service-fabric-package-apps.md#create-an-sfpkg). 

In diesem Tutorial verwenden wir die [Beispielanwendung „Voting“](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **Voting**, und wählen Sie dann **Packen** aus.

   ![Packen der Anwendung][PackageApplication]  
1. Wechseln Sie zum Verzeichnis *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug*. Zippen Sie den Inhalt in eine Datei mit dem Namen *Voting.zip*. Die Datei *ApplicationManifest.xml* muss sich im Stammverzeichnis der ZIP-Datei befinden.

   ![Zippen der Anwendung][ZipApplication]  
1. Benennen Sie die Erweiterung der Datei von „.zip“ in *.sfpkg* um.

1. Klicken Sie im Azure-Portal im Container **Apps** Ihres Speicherkontos auf **Hochladen**, und laden Sie dann **Voting.sfpkg** hoch. 

   ![Hochladen des App-Pakets][UploadAppPkg]

Nachdem das Staging für die Anwendung erfolgt ist, können Sie die Resource Manager-Vorlage für die Bereitstellung der Anwendung erstellen.

### <a name="create-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage

Die Beispielanwendung enthält [Azure Resource Manager-Vorlagen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM), die Sie zum Bereitstellen der Anwendung verwenden können. Die Vorlagendateien heißen *UserApp.json* und *UserApp.Parameters.json*.

> [!NOTE]
> Die Datei *UserApp.Parameters.json* muss mit dem Namen Ihres Clusters aktualisiert werden.
>
>


| Parameter              | BESCHREIBUNG                                 | Beispiel                                                      | Kommentare                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Der Name des Clusters, in dem die Bereitstellung erfolgt | sf-cluster123                                                |                                                              |
| application            | Der Name der Anwendung.                 | Voting                                                       |
| applicationTypeName    | Der Typname der Anwendung           | VotingType                                                   | Muss „ApplicationManifest.xml“ entsprechen                 |
| applicationTypeVersion | Die Version des Anwendungstyps         | 1.0.0                                                        | Muss „ApplicationManifest.xml“ entsprechen                 |
| serviceName            | Der Name des Diensts         | Voting~VotingWeb                                             | Muss im Format „ApplicationName~ServiceType“ vorliegen.            |
| serviceTypeName        | Der Typname des Diensts                | VotingWeb                                                    | Muss „ServiceManifest.xml“ entsprechen                 |
| appPackageUrl          | Die Blob Storage-URL der Anwendung     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Die URL des Anwendungspakets in Blob Storage (die Schritte zur Festlegung der URL werden weiter unten in diesem Artikel beschrieben) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Führen Sie das Cmdlet **New-AzResourceGroupDeployment** aus, um die Anwendung in der Ressourcengruppe bereitzustellen, die Ihren Cluster enthält:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Aktualisieren der Service Fabric-Anwendung mithilfe von Azure Resource Manager


> [!IMPORTANT]
> Per ARM-JSON-Definition bereitgestellte Dienste müssen aus dem Abschnitt „DefaultServices“ der entsprechenden Datei „ApplicationManifest.xml“ entfernt werden.


Sie müssen eine Anwendung, die bereits in einem Service Fabric-Cluster bereitgestellt wurde, ggf. aus einem dieser Gründe aktualisieren:

* Der Anwendung wird ein neuer Dienst hinzugefügt. Den Dateien *service-manifest.xml* und *application-manifest.xml* muss eine Dienstdefinition hinzugefügt werden, wenn ein Dienst zur Anwendung hinzugefügt wird. Um eine neue Version einer Anwendung widerzuspiegeln, müssen Sie auch in [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json) die Version des Anwendungstyps von 1.0.0 in 1.0.1 ändern:

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Eine neue Version eines vorhandenen Diensts wird der Anwendung hinzugefügt. Beispiele umfassen Änderungen am Anwendungscode sowie Aktualisierungen von Typversion und Namen der App. Aktualisieren Sie für dieses Upgrade „UserApp.Parameters.json“ wie folgt:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Löschen von Anwendungsressourcen

So löschen Sie eine Anwendung, die mithilfe des Anwendungsressourcenmodells in Resource Manager bereitgestellt wurde

1. Rufen Sie mit dem Cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-2.5.0) die Ressourcen-ID für die Anwendung ab:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Löschen Sie die Anwendungsressourcen mit dem Cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.5.0):

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anwendungsressourcenmodell:

* [Modellieren einer Anwendung in Service Fabric](service-fabric-application-model.md)
* [Service Fabric-Anwendungs- und -Dienstmanifeste](service-fabric-application-and-service-manifests.md)
* [Bewährte Methoden: Infrastructure-as-Code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Verwalten von Anwendungen und Diensten als Azure-Ressourcen](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
