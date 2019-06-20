---
title: Erstellen eines Service Fabric-Clusters mit Windows in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von PowerShell einen Service Fabric-Windows-Cluster in einem virtuellen Azure-Netzwerk und einer Netzwerksicherheitsgruppe bereitstellen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: dabbefa8ca2073e30948f1c70782f730bceae030
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158097"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Bereitstellen eines Service Fabric-Clusters mit Windows in einem virtuellen Azure-Netzwerk

Dieses Tutorial ist der erste Teil einer Serie. Es wird beschrieben, wie Sie einen Azure Service Fabric-Cluster mit Windows in einem [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) und einer [Netzwerksicherheitsgruppe](../virtual-network/virtual-networks-nsg.md) bereitstellen, indem Sie PowerShell und eine Vorlage verwenden. Wenn Sie fertig sind, verfügen Sie über einen Cluster, der in der Cloud ausgeführt wird und für den Sie Anwendungen bereitstellen können. Informationen zum Erstellen eines Linux-Clusters, der Azure CLI verwendet, finden Sie unter [Erstellen eines sicheren Linux-Clusters in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

In diesem Tutorial wird ein Produktionsszenario beschrieben. Wenn Sie einen kleinen Cluster für Testzwecke erstellen möchten, helfen Ihnen die Informationen unter [Erstellen von Service Fabric-Clustern](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) weiter.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines VNET in Azure mithilfe von PowerShell
> * Erstellen eines Schlüsseltresors und Hochladen eines Zertifikats
> * Einrichten der Azure Active Directory-Authentifizierung
> * Konfigurieren der Diagnosesammlung
> * Einrichten des EventStore-Diensts
> * Einrichten von Azure Monitor-Protokollen
> * Erstellen eines sicheren Service Fabric-Clusters in Azure PowerShell
> * Sichern des Clusters mit einem X.509-Zertifikat
> * Herstellen einer Verbindung mit dem Cluster über PowerShell
> * Entfernen eines Clusters

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines sicheren Clusters in Azure
> * [Überwachen eines Clusters](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontales Herunter- oder Hochskalieren eines Clusters](service-fabric-tutorial-scale-cluster.md)
> * [Aktualisieren der Runtime eines Clusters](service-fabric-tutorial-upgrade-cluster.md)
> * [Löschen eines Clusters](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.
* Installieren Sie das [Service Fabric SDK und das PowerShell-Modul](service-fabric-get-started.md).
* Installieren Sie [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Machen Sie sich mit den grundlegenden Konzepten von [Azure-Clustern](service-fabric-azure-clusters-overview.md) vertraut.
* [Planen und Vorbereiten](service-fabric-cluster-azure-deployment-preparation.md) der Bereitstellung eines Produktionsclusters

Mit den folgenden Schritten wird ein Service Fabric-Cluster mit sieben Knoten erstellt. Zum Berechnen der Kosten, die durch das Ausführen eines Service Fabric-Clusters in Azure anfallen, verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Herunterladen und Erkunden der Vorlage

Laden Sie die folgenden Azure Resource Manager-Vorlagendateien herunter:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Diese Vorlage stellt einen sicheren Cluster mit sieben virtuellen Computern und drei Knotentypen in einem virtuellen Netzwerk und einer Netzwerksicherheitsgruppe bereit.  Weitere Beispielvorlagen finden Sie auf [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [azuredeploy.json][template] stellt verschiedene Ressourcen bereit, einschließlich der folgenden.

### <a name="service-fabric-cluster"></a>Service Fabric-Cluster

In der Ressource **Microsoft.ServiceFabric/clusters** wird ein Windows-Cluster mit den folgenden Merkmalen konfiguriert:

* Drei Knotentypen.
* Fünf Knoten für den primären Knotentyp (in den Vorlagenparametern konfigurierbar) sowie jeweils ein Knoten für die anderen beiden Knotentypen.
* Betriebssystem: Windows Server 2016 Datacenter mit Containern (in den Vorlagenparametern konfigurierbar).
* Geschütztes Zertifikat (in den Vorlagenparametern konfigurierbar).
* [Reverseproxy](service-fabric-reverseproxy.md) aktiviert.
* [DNS-Dienst](service-fabric-dnsservice.md) aktiviert.
* [Dauerhaftigkeitsstufe](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) „Bronze“ (in den Vorlagenparametern konfigurierbar).
* [Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) „Silber“ (in den Vorlagenparametern konfigurierbar).
* Clientverbindungsendpunkt: 19000 (in den Vorlagenparametern konfigurierbar).
* HTTP-Gatewayendpunkt: 19080 (in den Vorlagenparametern konfigurierbar).

### <a name="azure-load-balancer"></a>Azure Load Balancer

In der Ressource **Microsoft.Network/loadBalancers** wird ein Lastenausgleich konfiguriert. Für die folgenden Ports werden Tests und Regeln eingerichtet:

* Clientverbindungsendpunkt: 19000
* HTTP-Gatewayendpunkt: 19080
* Anwendungsport: 80
* Anwendungsport: 443
* Service Fabric-Reverseproxy: 19081

Falls andere Anwendungsports benötigt werden, müssen Sie die Ressourcen **Microsoft.Network/loadBalancers** und **Microsoft.Network/networkSecurityGroups** anpassen, um den eingehenden Datenverkehr zuzulassen.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuelles Netzwerk, Subnetz und Netzwerksicherheitsgruppe

Die Namen des virtuellen Netzwerks, des Subnetzes und der Netzwerksicherheitsgruppe werden in den Vorlagenparametern deklariert. Adressräume des virtuellen Netzwerks und des Subnetzes werden auch in den Vorlagenparametern deklariert und in der Ressource **Microsoft.Network/virtualNetworks** konfiguriert:

* Adressraum des virtuellen Netzwerks: 172.16.0.0/20
* Service Fabric-Subnetzadressraum: 172.16.2.0/23

Die folgenden Regeln für eingehenden Datenverkehr sind für die Ressource **Microsoft.Network/networkSecurityGroups** aktiviert. Sie können die Portwerte ändern, indem Sie die Vorlagenvariablen ändern.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Kurzlebiger Portbereich: 49152 bis 65534 (mindestens 256 Ports)
* Ports für die Anwendungsverwendung: 80 und 443
* Portbereich für Anwendungen: 49152 bis 65534 (wird für die Kommunikation von Dienst zu Dienst verwendet. Es werden keine anderen Ports im Lastenausgleichsmodul geöffnet.)
* Blockieren aller anderen Ports

Falls andere Anwendungsports benötigt werden, müssen Sie die Ressourcen **Microsoft.Network/loadBalancers** und **Microsoft.Network/networkSecurityGroups** anpassen, um den eingehenden Datenverkehr zuzulassen.

### <a name="windows-defender"></a>Windows Defender
Das [Windows Defender-Antivirenprogramm](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) ist unter Windows Server 2016 standardmäßig installiert und betriebsbereit. Die Benutzeroberfläche wird bei einigen SKUs standardmäßig installiert, aber dies ist keine erforderliche Komponente. Für jeden in der Vorlage deklarierten Knotentyp bzw. für jede in der Vorlage deklarierte VM-Skalierungsgruppe wird die [Antimalware-Erweiterung für virtuelle Azure-Computer](/azure/virtual-machines/extensions/iaas-antimalware-windows) verwendet, um die Service Fabric-Verzeichnisse und -Prozesse auszuschließen:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Festlegen von Vorlagenparametern

In der Parameterdatei [azuredeploy.parameters][parameters] werden viele Werte deklariert, die zum Bereitstellen des Clusters und der zugehörigen Ressourcen verwendet werden. Im Folgenden finden Sie Parameter, die Sie für Ihre Bereitstellung ändern müssen:

**Parameter** | **Beispielwert** | **Hinweise** 
|---|---|---|
|adminUserName|vmadmin| Administratorbenutzername für die Cluster-VMs [Benutzernamenanforderungen für virtuelle Computer](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Administratorkennwort für die Cluster-VMs [Kennwortanforderungen für virtuelle Computer](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Name des Clusters Darf nur Buchstaben und Zahlen enthalten. Kann 3 bis 23 Zeichen enthalten.|
|location|southcentralus| Standort des Clusters |
|certificateThumbprint|| <p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird.</p><p>Um ein vorhandenes, zuvor in einen Schlüsseltresor hochgeladenes Zertifikat zu verwenden, geben Sie den Wert für den SHA-1-Zertifikatfingerabdruck ein. Beispiel: „6190390162C988701DB5676EB81083EA608DCCF3“.</p> |
|certificateUrlValue|| <p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird. </p><p>Geben Sie die Zertifikat-URL ein, wenn Sie ein vorhandenes Zertifikat verwenden möchten, das zuvor in einen Schlüsseltresor hochgeladen wurde. Beispiel: https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird.</p><p>Geben Sie den Wert für den Quelltresor ein, wenn Sie ein vorhandenes Zertifikat verwenden möchten, das zuvor in einen Schlüsseltresor hochgeladen wurde. Beispiel: „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT“.</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Einrichten der Azure Active Directory-Clientauthentifizierung
Für Service Fabric-Cluster, die in einem öffentlichen, in Azure gehosteten Netzwerk bereitgestellt werden, wird im Zusammenhang mit der gegenseitigen Client-zu-Knoten-Authentifizierung Folgendes empfohlen:
* Verwenden von Azure Active Directory für die Clientidentität.
* Verwenden eines Zertifikats für die Serveridentität und SSL-Verschlüsselung der HTTP-Kommunikation.

Das Einrichten von Azure Active Directory (Azure AD) zur Authentifizierung von Clients für einen Service Fabric-Cluster muss vor dem [Erstellen des Clusters](#createvaultandcert) durchgeführt werden. Mit Azure AD können Organisationen (so genannte Mandanten) den Benutzerzugriff auf Anwendungen verwalten. 

Service Fabric-Cluster bieten unterschiedliche Einstiegspunkte für ihre Verwaltungsfunktionen. Hierzu zählen etwa [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) (webbasiert) und [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Daher erstellen Sie zwei Azure AD-Anwendungen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung.  Nachdem die Anwendungen erstellt wurden, weisen Sie Benutzer zu schreibgeschützten Rollen und Administratorrollen zu.

> [!NOTE]
> Vor der Clustererstellung müssen folgende Schritte ausgeführt werden. Da in den Skripts Clusternamen und Endpunkte erwartet werden, sollte es sich bei den Werten nicht um bereits erstellte, sondern um geplante Werte handeln.

In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Mandanten erstellt haben. Lesen Sie andernfalls zunächst den Artikel [Einrichten eines Azure Active Directory-Mandanten](../active-directory/develop/quickstart-create-new-tenant.md).

Wir haben eine Reihe von Windows PowerShell-Skripts erstellt, um Schritte der Konfiguration von Azure AD mit einem Service Fabric-Cluster zu vereinfachen. [Laden Sie die Skripts auf Ihren Computer herunter.](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Erstellen von Azure AD-Anwendungen und Zuweisen von Benutzern zu Rollen
Erstellen Sie zwei Azure AD-Anwendungen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung. Nachdem Sie die Anwendungen für Ihren Cluster erstellt haben, müssen Ihre Benutzer den [von Service Fabric unterstützten Rollen](service-fabric-cluster-security-roles.md) zugewiesen werden: „read-only“ (schreibgeschützt) und „admin“ (Administrator).

Führen Sie `SetupApplications.ps1` aus, und geben Sie die Mandanten-ID, den Clusternamen und die Antwort-URL der Webanwendung als Parameter an. Geben Sie Benutzernamen und Kennwörter für die Benutzer an. Beispiel: 

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Für nationale Clouds (etwa Azure Government, Azure China oder Azure Deutschland) geben Sie den Parameter `-Location` an.

Ihre Mandanten-ID (*TenantId*) oder Verzeichnis-ID finden Sie im [Azure-Portal](https://portal.azure.com). Wählen Sie **Azure Active Directory** > **Eigenschaften** aus, und kopieren Sie den Wert der **Verzeichnis-ID**.

*ClusterName* wird als Präfix für durch das Skript erstellte Azure AD-Anwendungen verwendet. Der Wert muss nicht exakt dem tatsächlichen Clusternamen entsprechen. Dies macht es lediglich einfacher, dem in Verwendung befindlichen Service Fabric-Cluster Azure AD-Artefakte zuzuordnen.

*WebApplicationReplyUrl* ist der Standardendpunkt, den Azure AD nach Abschluss des Anmeldeprozesses an Ihre Benutzer zurückgibt. Legen Sie diesen Endpunkt auf den Service Fabric Explorer-Endpunkt für Ihren Cluster fest. Dies ist standardmäßig folgender Wert:

https://&lt;Cluster-Domäne&gt;:19080/Explorer

Sie werden aufgefordert, sich bei einem Konto anzumelden, das über Administratorrechte für den Azure AD-Mandanten verfügt. Nach der Anmeldung erstellt das Skript die Webanwendung und die native Anwendung, die Ihren Service Fabric-Cluster darstellen. In den Anwendungen des Mandanten im [Azure-Portal](https://portal.azure.com) sollten zwei neue Einträge angezeigt werden:

   * *Clustername*\_Cluster
   * *Clustername*\_Client

Das Skript gibt den für die Resource Manager-Vorlage erforderlichen JSON-Code aus, wenn Sie den Cluster erstellen. Es empfiehlt sich daher, das PowerShell-Fenster geöffnet zu lassen.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Hinzufügen der Azure AD-Konfiguration zum Verwenden von Azure AD für den Clientzugriff
Konfigurieren Sie Azure AD in der Datei [azuredeploy.json][template] im Abschnitt **Microsoft.ServiceFabric/clusters**. Fügen Sie Parameter für die Mandanten-ID, die Clusteranwendungs-ID und die Clientanwendungs-ID hinzu.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Fügen Sie die Parameterwerte in der Parameterdatei [azuredeploy.parameters.json][parameters] hinzu. Beispiel: 

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Konfigurieren der Diagnosesammlung auf dem Cluster
Bei Verwendung eines Service Fabric-Clusters empfiehlt es sich, die Protokolle aller Knoten an einem zentralen Ort zu sammeln. Das Sammeln der Protokolle an einem zentralen Ort hilft Ihnen bei Analyse und Behandlung von Problemen, die ggf. in Ihrem Cluster oder in den Anwendungen und Diensten des Clusters auftreten.

Eine Möglichkeit zum Hochladen und Sammeln von Protokollen ist die Verwendung der Azure-Diagnose (WAD)-Erweiterung, mit der Protokolle in Azure Storage hochgeladen und an Azure Application Insights oder Event Hubs gesendet werden können. Sie können zudem einen externen Prozess verwenden, um die Ereignisse aus dem Speicher zu lesen und in einem Analyseplattformprodukt wie Azure Monitor-Protokolle oder in einer anderen Protokollanalyselösung zu verwenden.

Wenn Sie dieses Tutorial durcharbeiten, ist die Diagnosesammlung bereits in der [Vorlage][template] konfiguriert.

Wenn Sie bereits über einen Cluster ohne Diagnosebereitstellung verfügen, können Sie sie mithilfe der Clustervorlage hinzufügen oder den Cluster aktualisieren. Ändern Sie die zum Erstellen des vorhandenen Clusters verwendete Resource Manager-Vorlage, oder laden Sie die Vorlage aus dem Portal herunter. Ändern Sie die Datei „template.json“ wie folgt:

Fügen Sie dem Ressourcenabschnitt in der Vorlage eine neue Speicherressource hinzu:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Fügen Sie als Nächstes Parameter für den Speicherkontonamen und -typ im Abschnitt „Parameter“ der Vorlage hinzu. Ersetzen Sie den Platzhaltertext „storage account name goes here“ durch den Namen des gewünschten Speicherkontos.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Fügen Sie als Nächstes die **IaaSDiagnostics**-Erweiterung dem Array der Erweiterungen der Eigenschaft **VirtualMachineProfile** jeder **Microsoft.Compute/virtualMachineScaleSets**-Ressource im Cluster hinzu.  Wenn Sie die [Beispielvorlage][template] verwenden, sind dort drei VM-Skalierungsgruppen vorhanden (eine für jeden Knoten im Cluster).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Konfigurieren des EventStore-Diensts
Der EventStore-Dienst ist eine Überwachungsoption in Service Fabric. EventStore bietet eine Möglichkeit, den Zustand Ihres Clusters oder Ihrer Workloads zu einem bestimmten Zeitpunkt zu verstehen. EventStore ist ein zustandsbehafteter Service Fabric-Dienst, der Ereignisse vom Cluster verwaltet. Die Ereignisse werden über Service Fabric-Explorer, REST und APIs bereitgestellt. EventStore fragt den Cluster direkt ab, um Diagnosedaten zu einer beliebigen Entität im Cluster abzurufen. Diese Daten können Sie bei den folgenden Aufgaben unterstützen:

* Diagnostizieren von Problemen bei der Entwicklung oder bei Tests oder an den Punkten, an denen Sie möglicherweise eine Überwachungspipeline verwenden.
* Sicherstellen, dass am Cluster vorgenommene Verwaltungsaktionen ordnungsgemäß verarbeitet werden.
* Abrufen einer „Momentaufnahme“ der Interaktion von Service Fabric mit der betreffenden Entität.



Um den EventStore-Dienst in Ihrem Cluster zu aktivieren, fügen Sie Folgendes der Eigenschaft **fabricSettings** der **Microsoft.ServiceFabric/clusters**-Ressource hinzu:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Einrichten von Azure Monitor-Protokollen für den Cluster

Azure Monitor-Protokolle sind unsere Empfehlung für die Überwachung von Ereignissen auf Clusterebene. Zum Einrichten von Azure Monitor-Protokollen zur Überwachung des Clusters muss die [Diagnose aktiviert sein, um Ereignisse auf Clusterebene anzeigen zu können](#configure-diagnostics-collection-on-the-cluster).  

Der Arbeitsbereich muss mit den Diagnosedaten aus Ihrem Cluster verknüpft werden.  Diese Protokolldaten werden im Speicherkonto *applicationDiagnosticsStorageAccountName* in den Tabellen „WADServiceFabric*EventTable“, „WADWindowsEventLogsTable“ und „WADETWEventTable“ gespeichert.

Fügen Sie zuerst den Azure Log Analytics-Arbeitsbereich hinzu und dann die Lösung zu diesem Arbeitsbereich:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Fügen Sie als Nächstes Parameter hinzu.
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Fügen Sie als Nächstes Variablen hinzu:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Fügen Sie die Log Analytics-Agent-Erweiterung jeder VM-Skalierungsgruppe im Cluster hinzu, und verbinden Sie den Agent mit dem Log Analytics-Arbeitsbereich. Dies ermöglicht die Erfassung von Diagnosedaten zu Containern, Anwendungen und zur Leistungsüberwachung. Durch Hinzufügen als Erweiterung der VM-Skalierungsgruppenressource stellt Azure Resource Manager sicher, dass der Agent auf jedem Knoten installiert wird – auch beim Skalieren des Clusters.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Bereitstellen des virtuellen Netzwerks und des Clusters

Richten Sie als Nächstes die Netzwerktopologie ein, und stellen Sie den Service Fabric-Cluster bereit. Mit der Resource Manager-Vorlage [azuredeploy.json][template] werden ein virtuelles Netzwerk, ein Subnetz und eine Netzwerksicherheitsgruppe für Service Fabric erstellt. Außerdem wird mit der Vorlage ein Cluster mit aktivierter Zertifikatsicherheit bereitgestellt. Verwenden Sie für Produktionscluster ein Zertifikat einer Zertifizierungsstelle als Clusterzertifikat. Es kann ein selbstsigniertes Zertifikat zum Schützen von Testclustern verwendet werden.

Mit der Vorlage in diesem Artikel wird ein Cluster bereitgestellt, der das Clusterzertifikat anhand des Zertifikatfingerabdrucks identifiziert. Zwei Zertifikate können nicht den gleichen Fingerabdruck haben, was die Zertifikatverwaltung erschwert. Wenn Sie einen bereitgestellten Cluster von Zertifikatfingerabdrücken auf allgemeine Zertifikatnamen umstellen, vereinfacht dies die Zertifikatverwaltung. Unter [Ändern des Clusters in Zertifikatverwaltung mit allgemeinen Namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) erfahren Sie, wie Sie den Cluster aktualisieren, um allgemeine Zertifikatnamen für die Zertifikatverwaltung zu verwenden.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Erstellen eines Clusters mithilfe eines vorhandenen Zertifikats

Im folgenden Skript werden das Cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) und eine Vorlage verwendet, um einen neuen Cluster in Azure bereitzustellen. Das Cmdlet erstellt einen neuen Schlüsseltresor in Azure und lädt Ihr Zertifikat hoch.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Erstellen eines Clusters mithilfe eines neuen selbstsignierten Zertifikats

Im folgenden Skript werden das Cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) und eine Vorlage verwendet, um einen neuen Cluster in Azure bereitzustellen. Das Cmdlet erstellt einen neuen Schlüsseltresor in Azure, fügt diesem ein neues selbstsigniertes Zertifikat hinzu und lädt das Zertifikat an einen lokalen Speicherort herunter.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Herstellen einer Verbindung mit dem sicheren Cluster

Stellen Sie mithilfe des Service Fabric-PowerShell-Moduls, das zusammen mit dem Service Fabric SDK installiert wird, eine Verbindung mit dem Cluster her.  Zunächst installieren Sie das Zertifikat im persönlichen Speicher des aktuellen Benutzers auf Ihrem Computer. Führen Sie den folgenden PowerShell-Befehl aus:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Sie können nun die Verbindung mit Ihrem sicheren Cluster herstellen.

Das **Service Fabric**-PowerShell-Modul bietet viele Cmdlets zum Verwalten von Service Fabric-Clustern, Anwendungen und Diensten. Verwenden Sie das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster), um eine Verbindung mit dem Cluster herzustellen. Die Informationen zum SHA-1-Zertifikatfingerabdruck und zum Verbindungsendpunkt finden Sie in der Ausgabe des vorherigen Schritts.

Wenn Sie die Azure AD-Clientauthentifizierung bereits eingerichtet haben, führen Sie folgenden Befehl aus: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Wenn Sie die Azure AD-Clientauthentifizierung nicht eingerichtet haben, führen Sie folgenden Befehl aus:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Prüfen Sie mithilfe des Cmdlets [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth), ob Sie verbunden sind und der Cluster fehlerfrei ist.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den anderen Artikeln dieser Tutorialreihe wird der erstellte Cluster verwendet. Wenn Sie nicht direkt mit dem nächsten Artikel fortfahren, sollten Sie [den Cluster löschen](service-fabric-cluster-delete.md), um anfallende Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Ihren Cluster skalieren.

> [!div class="checklist"]
> * Erstellen eines VNET in Azure mithilfe von PowerShell
> * Erstellen eines Schlüsseltresors und Hochladen eines Zertifikats
> * Einrichten der Azure Active Directory-Authentifizierung
> * Konfigurieren der Diagnosesammlung
> * Einrichten des EventStore-Diensts
> * Einrichten von Azure Monitor-Protokollen
> * Erstellen eines sicheren Service Fabric-Clusters in Azure PowerShell
> * Sichern des Clusters mit einem X.509-Zertifikat
> * Herstellen einer Verbindung mit dem Cluster über PowerShell
> * Entfernen eines Clusters

Fahren Sie als Nächstes mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Ihren Cluster überwachen.
> [!div class="nextstepaction"]
> [Überwachen eines Clusters](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
