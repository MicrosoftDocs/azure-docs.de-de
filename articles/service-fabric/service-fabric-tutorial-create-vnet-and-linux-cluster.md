---
title: Erstellen eines Service Fabric-Clusters unter Linux in Azure
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI einen Service Fabric-Linux-Cluster in einem vorhandenen virtuellen Azure-Netzwerk bereitstellen.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 52eba2e5780b1a66f3884a764631908335372273
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738951"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Bereitstellen eines Service Fabric-Linux-Clusters in einem virtuellen Azure-Netzwerk

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI und einer Vorlage einen Service Fabric-Linux-Cluster in einem [virtuellen Azure-Netzwerk (VNET)](../virtual-network/virtual-networks-overview.md) bereitstellen. Wenn Sie fertig sind, verfügen Sie über einen Cluster, der in der Cloud ausgeführt wird und für den Sie Anwendungen bereitstellen können. Informationen zum Erstellen eines Windows-Clusters mithilfe von PowerShell finden Sie unter [Bereitstellen eines sicheren Service Fabric-Windows-Clusters in einem virtuellen Azure-Netzwerk](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitungen

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installieren Sie die [Service Fabric CLI](service-fabric-cli.md).
* Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli).
* Die grundlegenden Konzepte von Clustern finden Sie unter [Übersicht über Azure-Cluster](service-fabric-azure-clusters-overview.md).
* Führen Sie die Schritte zum [Planen und Vorbereiten](service-fabric-cluster-azure-deployment-preparation.md) der Bereitstellung eines Produktionsclusters aus.

Mit den folgenden Schritten wird ein Service Fabric-Cluster mit sieben Knoten erstellt. Zum Berechnen der Kosten, die durch das Ausführen eines Service Fabric-Clusters in Azure anfallen, verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Herunterladen und Erkunden der Vorlage

Laden Sie die folgenden Resource Manager-Vorlagendateien herunter:

Für Ubuntu 16.04 LTS:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Für Ubuntu 18.04 LTS:

* [AzureDeploy.json][template2]
* [AzureDeploy.Parameters.json][parameters2]

Für Ubuntu 18.04 LTS umfasst der Unterschied zwischen den beiden Vorlagen Folgendes: 
* Attribut **vmImageSku** ist auf „18.04-LTS“ festgelegt
* **typeHandlerVersion** -Element jedes Knotens ist auf 1.1 festgelegt
* Für die Ressource „Microsoft.ServiceFabric/clusters“:
   - **apiVersion** ist auf „2019-03-01“ oder höher festgelegt
   - **vmImage** -Eigenschaft ist auf „Ubuntu18_04“ festgelegt

Diese Vorlage stellt einen sicheren Cluster mit sieben virtuellen Computern und drei Knotentypen in einem virtuellen Netzwerk bereit.  Weitere Beispielvorlagen finden Sie auf [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Die Datei [AzureDeploy.json][template] stellt verschiedene Ressourcen bereit, einschließlich der folgenden.

### <a name="service-fabric-cluster"></a>Service Fabric-Cluster

In der Ressource **Microsoft.ServiceFabric/clusters** wird ein Linux-Cluster mit den folgenden Eigenschaften bereitgestellt:

* Drei Knotentypen
* Fünf Knoten für den primären Knotentyp (in den Vorlagenparametern konfigurierbar) sowie jeweils ein Knoten für die anderen Knotentypen
* Betriebssystem: (Ubuntu 16.04 LTS / Ubuntu 18.04 LTS) (in den Vorlagenparametern konfigurierbar)
* Geschütztes Zertifikat (in den Vorlagenparametern konfigurierbar)
* [DNS-Dienst](service-fabric-dnsservice.md) aktiviert
* [Dauerhaftigkeitsstufe](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) „Bronze“ (in den Vorlagenparametern konfigurierbar)
* [Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) „Silber“ (in den Vorlagenparametern konfigurierbar)
* Clientverbindungsendpunkt: 19000 (in den Vorlagenparametern konfigurierbar)
* HTTP-Gatewayendpunkt: 19080 (in den Vorlagenparametern konfigurierbar)

### <a name="azure-load-balancer"></a>Azure Load Balancer

In der Ressource **Microsoft.Network/loadBalancers** wird ein Lastenausgleich konfiguriert, und es werden Tests und Regeln für die folgenden Ports eingerichtet:

* Clientverbindungsendpunkt: 19000
* HTTP-Gatewayendpunkt: 19080
* Anwendungsport: 80
* Anwendungsport: 443

### <a name="virtual-network-and-subnet"></a>Virtuelles Netzwerk und Subnetz

Die Namen des virtuellen Netzwerks und Subnetzes werden in den Vorlagenparametern deklariert.  Adressräume des virtuellen Netzwerks und des Subnetzes werden auch in den Vorlagenparametern deklariert und in der Ressource **Microsoft.Network/virtualNetworks** konfiguriert:

* Adressraum des virtuellen Netzwerks: 10.0.0.0/16
* Service Fabric-Subnetzadressraum: 10.0.2.0/24

Wenn keine anderen Anwendungsports benötigt werden, müssen Sie die Ressource „Microsoft.Network/loadBalancers“ anpassen, um eingehenden Datenverkehr zuzulassen.

## <a name="set-template-parameters"></a>Festlegen von Vorlagenparametern

In der **AzureDeploy.Parameters** -Datei werden viele Werte deklariert, die zum Bereitstellen des Clusters und der zugehörigen Ressourcen verwendet werden. Hier sind einige Parameter angegeben, die Sie für Ihre Bereitstellung ggf. ändern müssen:

|Parameter|Beispielwert|Notizen|
|---|---||
|adminUserName|vmadmin| Administratorbenutzername für die Cluster-VMs |
|adminPassword|Password#1234| Administratorkennwort für die Cluster-VMs|
|clusterName|mysfcluster123| Name des Clusters |
|location|southcentralus| Standort des Clusters |
|certificateThumbprint|| <p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird.</p><p>Um ein vorhandenes, zuvor in einen Schlüsseltresor hochgeladenes Zertifikat zu verwenden, geben Sie den Wert für den SHA-1-Zertifikatfingerabdruck ein. Beispiel: „6190390162C988701DB5676EB81083EA608DCCF3“. </p>|
|certificateUrlValue|| <p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird.</p><p>Geben Sie die Zertifikat-URL ein, wenn Sie ein vorhandenes Zertifikat verwenden möchten, das zuvor in einen Schlüsseltresor hochgeladen wurde. Beispiel: https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Der Wert sollte leer sein, wenn ein selbstsigniertes Zertifikat erstellt oder eine Zertifikatsdatei bereitgestellt wird.</p><p>Geben Sie den Wert für den Quelltresor ein, wenn Sie ein vorhandenes Zertifikat verwenden möchten, das zuvor in einen Schlüsseltresor hochgeladen wurde. Beispiel: „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT“.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Bereitstellen des virtuellen Netzwerks und des Clusters

Richten Sie als Nächstes die Netzwerktopologie ein, und stellen Sie den Service Fabric-Cluster bereit. Die Resource Manager-Vorlage **AzureDeploy.json** erstellt ein virtuelles Netzwerk (VNET) und ein Subnetz für Service Fabric. Außerdem wird mit der Vorlage ein Cluster mit aktivierter Zertifikatsicherheit bereitgestellt.  Verwenden Sie für Produktionscluster ein Zertifikat einer Zertifizierungsstelle als Clusterzertifikat. Es kann ein selbstsigniertes Zertifikat zum Schützen von Testclustern verwendet werden.

Mit der Vorlage in diesem Artikel wird ein Cluster bereitgestellt, der das Clusterzertifikat anhand des Zertifikatfingerabdrucks identifiziert.  Zwei Zertifikate können nicht den gleichen Fingerabdruck haben, was die Zertifikatverwaltung erschwert. Durch den Wechsel von „Zertifikatfingerabdruck“ zu „Allgemeiner Name“ bei einem bereitgestellten Cluster wird die Zertifikatverwaltung vereinfacht.  Unter [Ändern des Clusters in Zertifikatverwaltung mit allgemeinen Namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) erfahren Sie, wie Sie den Cluster aktualisieren, um allgemeine Zertifikatnamen für die Zertifikatverwaltung zu verwenden.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Erstellen eines Clusters mit einem vorhandenen Zertifikat

Im folgenden Skript werden der Befehl [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) und eine Vorlage verwendet, um einen neuen Cluster bereitzustellen, der durch ein vorhandenes Zertifikat geschützt ist. Außerdem wird mit dem Befehl ein neuer Schlüsseltresor in Azure erstellt und Ihr Zertifikat hochgeladen.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Erstellen eines Clusters mit einem neuen selbstsignierten Zertifikat

Das folgende Skript verwendet den Befehl [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) und eine Vorlage, um einen neuen Cluster in Azure bereitzustellen. Der Befehl erstellt auch einen neuen Schlüsseltresor in Azure, fügt diesem ein neues selbstsigniertes Zertifikat hinzu und lädt das Zertifikat an einen lokalen Speicherort herunter.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Herstellen einer Verbindung mit dem sicheren Cluster

Stellen Sie mit dem Service Fabric-CLI-Befehl `sfctl cluster select` unter Verwendung Ihres Schlüssels eine Verbindung mit dem Cluster her.  Verwenden Sie für ein selbstsigniertes Zertifikat nur die Option **--no-verify** .

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Überprüfen Sie mithilfe des Befehls `sfctl cluster health`, ob die Verbindung hergestellt wurde und der Cluster fehlerfrei ist.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht direkt mit dem nächsten Artikel fortfahren, sollten Sie [den Cluster löschen](./service-fabric-tutorial-delete-cluster.md), um anfallende Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [einen Cluster skalieren](service-fabric-tutorial-scale-cluster.md).

Mit der Vorlage in diesem Artikel wird ein Cluster bereitgestellt, der das Clusterzertifikat anhand des Zertifikatfingerabdrucks identifiziert.  Zwei Zertifikate können nicht den gleichen Fingerabdruck haben, was die Zertifikatverwaltung erschwert. Durch den Wechsel von „Zertifikatfingerabdruck“ zu „Allgemeiner Name“ bei einem bereitgestellten Cluster wird die Zertifikatverwaltung vereinfacht.  Unter [Ändern des Clusters in Zertifikatverwaltung mit allgemeinen Namen](service-fabric-cluster-change-cert-thumbprint-to-cn.md) erfahren Sie, wie Sie den Cluster aktualisieren, um allgemeine Zertifikatnamen für die Zertifikatverwaltung zu verwenden.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
