---
title: Verwenden von Azure Kubernetes mit Azure Cosmos DB
description: Erfahren Sie, wie ein Bootstrap für einen Kubernetes-Cluster in Azure ausgeführt wird, für den Azure Cosmos DB (Vorschauversion) verwendet wird.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 2c6af53aeec5d40f603d65595d93527107c0d80a
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427702"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Verwenden von Azure Kubernetes mit Azure Cosmos DB (Vorschauversion)

Dank der etcd-API in Azure Cosmos DB können Sie Azure Cosmos DB als Back-End-Speicher für Azure Kubernetes verwenden. Azure Cosmos DB implementiert das etcd-Wire Protocol, das es den API-Servern des Masterknotens ermöglicht, Azure Cosmos DB so zu verwenden, als würde dieser Dienst auf eine lokal installierte etcd-Instanz zugreifen. Die etcd-API in Azure Cosmos DB befindet sich aktuell in der Vorschauphase. Wenn Sie die Azure Cosmos-etcd-API als Sicherungsspeicher für Kubernetes verwenden, ergeben sich die folgenden Vorteile für Sie: 

* Keine Notwendigkeit, etcd manuell zu konfigurieren und zu verwalten
* Hohe Verfügbarkeit von etcd, garantiert durch Cosmos (99,99 % in einer einzigen Region, 99,999 % in mehreren Regionen)
* Elastische Skalierbarkeit von etcd
* Standardmäßig sicher und bereit für den Unternehmenseinsatz
* Branchenweit führende, umfassende SLAs

Weitere Informationen über die etcd-API in Azure Cosmos DB finden Sie im [Übersichtsartikel](etcd-api-introduction.md). In diesem Artikel wird gezeigt, wie Sie [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) dazu verwenden, einen Bootstrap für einen Kubernetes-Cluster in Azure auszuführen, für den [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) anstelle einer lokal installierten und konfigurierten etcd-Instanz verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

1. Installieren Sie die neueste Version von [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Sie können eine speziell für Ihr Betriebssystem bestimmte Azure CLI herunterladen und installieren.

1. Installieren Sie die [aktuelle Version](https://github.com/Azure/aks-engine/releases) von Azure Kubernetes Engine. Die Installationsanweisungen für verschiedene Betriebssysteme sind auf der Seite [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) verfügbar. Sie benötigen lediglich die Schritte aus dem Abschnitt **Install AKS Engine** des verknüpften Dokuments. Nach dem Herunterladen extrahieren Sie die ZIP-Datei.

   Die Azure Kubernetes Engine (**aks-engine**) generiert Azure Resource Manager-Vorlagen für Kubernetes-Cluster in Azure. Die Eingabe für aks-engine ist eine Clusterdefinitionsdatei, in der der gewünschte Cluster beschrieben ist, einschließlich Orchestrator, Funktionen und Agents. Die Struktur der Eingabedateien ist der öffentlichen API für Azure Kubernetes Service sehr ähnlich.

1. Die etcd-API in Azure Cosmos DB liegt aktuell in einer Vorschauversion vor. Wenn Sie die Vorschauversion verwenden möchten, melden Sie sich an unter: https://aka.ms/cosmosetcdapi-signup. Nachdem Sie das Formular übermittelt haben, wird Ihr Abonnement in die Whitelist aufgenommen, damit die etcd-API von Azure Cosmos verwendet werden kann. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Bereitstellen des Clusters mit Azure Cosmos DB

1. Öffnen Sie ein Eingabeaufforderungsfenster, und melden Sie sich mit dem folgenden Befehl bei Azure an:

   ```azurecli-interactive
   az login 
   ```

1. Wenn Sie mehrere Abonnements haben, wechseln Sie zu dem Abonnement, das für die etcd-API von Azure Cosmos DB in die Whitelist aufgenommen wurde. Mit dem folgenden Befehl können Sie zum erforderlichen Abonnement wechseln:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Erstellen Sie als Nächstes eine neue Ressourcengruppe, in der Sie die Ressourcen bereitstellen, die für den Azure Kubernetes-Cluster erforderlich sind. Erstellen Sie die Ressourcengruppe unbedingt in der Region „USA, Mitte“ (centralus). Es ist zwar nicht zwingend, dass sich die Ressourcengruppe in der Region „centralus“ befindet, die etcd-API von Azure Cosmos kann derzeit jedoch nur in der Region „centralus“ bereitgestellt werden. Daher empfiehlt es sich, den Kubernetes-Cluster und die Cosmos-etcd-Instanz in derselben Region anzuordnen:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Im nächsten Schritt erstellen Sie einen Dienstprinzipal für den Azure Kubernetes-Cluster, damit dieser mit den Ressourcen kommunizieren kann, die zur selben Ressourcengruppe gehören. Sie können einen Dienstprinzipal über Azure CLI, PowerShell oder das Azure-Portal erstellen. In diesem Beispiel verwenden Sie dazu CLI.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Dieser Befehl gibt die Details des Dienstprinzipals aus. Beispiel:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Notieren Sie sich die Werte der Felder **appId** und **password**, weil Sie diese Parameter in den nächsten Schritten benötigen. 

1. Navigieren Sie über die Eingabeaufforderung zu dem Ordner, in dem sich die ausführbare Datei von Azure Kubernetes Engine befindet. Beispielsweise können Sie an der Eingabeaufforderung zu diesem Ordner navigieren:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Öffnen Sie den von Ihnen bevorzugten Text-Editor, und definieren Sie eine Resource Manager-Vorlage, die den Azure Kubernetes-Clusters mit der etcd-API von Azure Cosmos DB bereitstellt. Kopieren Sie die folgende JSON-Definition in den Text-Editor, und speichern Sie die Datei als `apiModel.json`:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   Der in der JSON-/Clusterdefinitionsdatei zu beachtende Schlüsselparameter ist **"cosmosEtcd": true**. Dieser Parameter befindet sich in den „MasterProfile“-Eigenschaften, und er gibt für die Bereitstellung an, dass die Azure Cosmos-etcd-API anstelle der normalen etcd-Instanz verwendet werden soll. 

1. Stellen Sie den Azure Kubernetes-Cluster, für den Azure Cosmos DB verwendet wird, mit dem folgenden Befehl bereit:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Für Azure Kubernetes Engine wird eine Clusterdefinition genutzt, in der die gewünschte Form, Größe und Konfiguration des Azure Kubernetes-Clusters beschrieben sind. Es gibt mehrere Features, die über die Clusterdefinition aktiviert werden können. In diesem Beispiel verwenden Sie die folgenden Parameter:

   * **subscription-id:** Azure-Abonnement-ID, für die die etcd-API von Azure Cosmos DB aktiviert ist.
   * **client-id:** Die appId des Dienstprinzipals. Die `appId` wurde als Ausgabe in Schritt 4 zurückgegeben.
   * **client-secret:** Der Kennwort des Dienstprinzipals oder ein zufällig generiertes Kennwort. Dieser Wert wurde als Ausgabe im „password“-Parameter in Schritt 4 zurückgegeben. 
   * **dnsPrefix:** Ein für die Region eindeutiger DNS-Name. Dieser Wert bildet einen Teil des Hostnamens (Beispielwerte sind „meinprod1“, „staging“).
   * **location:**  Standort, in dem der Cluster bereitgestellt werden soll. Derzeit wird nur „centralus“ unterstützt.

   > [!Note]
   > Die Azure Cosmos-etcd-API kann derzeit nur in der Region „centralus“ bereitgestellt werden. 
 
   * **api-model:** Der vollqualifizierte Pfad zur Vorlagendatei.
   * **force-overwrite:** Diese Option wird verwendet, um im Ausgabeverzeichnis vorhandene Dateien automatisch zu überschreiben.
 
   Im folgenden Befehl ist eine Beispielbereitstellung gezeigt:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Die Vorlagenbereitstellung kann einige Minuten in Anspruch nehmen. Nachdem die Bereitstellung erfolgreich abgeschlossen wurde, sehen Sie die folgende Ausgabe in der Eingabeaufforderung:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Die Ressourcengruppe enthält jetzt Ressourcen, etwa einen virtuellen Computer, ein Azure Cosmos-Konto (etcd-API), ein virtuelles Netzwerk, eine Verfügbarkeitsgruppe und weitere Ressourcen, die für den Kubernetes-Cluster erforderlich sind. 

Der Name des Azure Cosmos-Kontos entspricht dem von Ihnen angegebenen DNS-Präfix mit angefügtem „k8s“. Ihr Azure Cosmos-Konto wird automatisch mit einer Datenbank namens **EtcdDB** und einem Container namens **EtcdData** bereitgestellt. In dem Container werden alle etcd-bezogenen Daten gespeichert. Der Container wird mit einer bestimmten Anzahl von Anforderungseinheiten bereitgestellt, und Sie können, entsprechend Ihres Workloads, [den Durchsatz skalieren (vergrößern/verkleinern)](scaling-throughput.md). 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [mit Azure Cosmos-Datenbanken, -Containern und -Elementen arbeiten](databases-containers-items.md) können.
* Erfahren Sie, wie Sie [die Kosten für den bereitgestellten Durchsatz optimieren](optimize-cost-throughput.md) können.

