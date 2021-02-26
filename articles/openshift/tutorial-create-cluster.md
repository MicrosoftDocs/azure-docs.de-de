---
title: 'Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters'
description: Erfahren Sie, wie Sie einen Microsoft Azure Red Hat OpenShift-Cluster mithilfe der Azure-Befehlszeilenschnittstelle erstellen
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 10/26/2020
ms.openlocfilehash: e6be2b659223fb110d7e13b14d732561df9ad408
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072235"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters

In diesem Tutorial (Teil 1 von 3) bereiten Sie Ihre Umgebung auf die Erstellung eines Azure Red Hat OpenShift-Clusters mit OpenShift 4 vor und erstellen einen Cluster. Sie lernen Folgendes:
> [!div class="checklist"]
> * Einrichten der Voraussetzungen 
> * Erstellen des erforderlichen virtuellen Netzwerks und der Subnetze
> * Bereitstellen eines Clusters

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.6.0 ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Für Azure Red Hat OpenShift sind mindestens 40 Kerne erforderlich, um einen OpenShift-Cluster zu erstellen und auszuführen. Das standardmäßige Azure-Ressourcenkontingent für ein neues Azure-Abonnement erfüllt diese Anforderung nicht. Weitere Informationen zum Anfordern einer Erhöhung des Ressourcenlimits finden Sie unter [Standardkontingent: Erhöhen der Grenzwerte nach VM-Serie](../azure-portal/supportability/per-vm-quota-requests.md) beschrieben.

### <a name="verify-your-permissions"></a>Überprüfen von Berechtigungen

Während dieses Tutorials werden Sie eine Ressourcengruppe erstellen, die das virtuelle Netzwerk für den Cluster enthält. Sie müssen entweder über die Berechtigungen „Mitwirkender“ und „Benutzerzugriffsadministrator“ oder über „Besitzer“-Berechtigungen verfügen, entweder direkt im virtuellen Netzwerk oder in der Ressourcengruppe bzw. im Abonnement, die diese enthalten.

Sie benötigen außerdem ausreichende Azure Active Directory-Berechtigungen für die Tools, um in Ihrem Auftrag ein Anwendungs- und Dienstprinzipal für den Cluster zu erstellen.

### <a name="register-the-resource-providers"></a>Registrieren der Ressourcenanbieter

1. Wenn Sie über mehrere Azure-Abonnements verfügen, geben Sie die gewünschte Abonnement-ID an:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrieren des `Microsoft.RedHatOpenShift`-Ressourcenanbieters

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. Registrieren des `Microsoft.Compute`-Ressourcenanbieters

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. Registrieren des `Microsoft.Storage`-Ressourcenanbieters

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Abrufen eines Red Hat-Pullgeheimnisses (optional)

Das Red Hat-Pullgeheimnis ermöglicht dem Cluster den Zugriff auf Red Hat-Containerregistrierungen zusammen mit zusätzlichem Inhalt. Dieser Schritt ist optional, wird aber empfohlen.

1. [Navigieren Sie zu Ihrem Manager-Portal des Red Hat OpenShift Clusters](https://cloud.redhat.com/openshift/install/azure/aro-provisioned), und melden Sie sich an.

   Sie müssen sich bei Ihrem Red Hat-Konto anmelden oder ein neues Red Hat-Konto mit Ihrer Geschäfts-E-Mail-Adresse erstellen und den Geschäftsbedingungen zustimmen.

1. Klicken Sie auf **Download pull secret** (Pullgeheimnis herunterladen), und laden Sie ein Pullgeheimnis zur Verwendung mit Ihrem ARO-Cluster herunter.

    Speichern Sie die Datei `pull-secret.txt` an einem sicheren Ort. Die Datei wird bei jeder Clustererstellung verwendet, wenn Sie einen Cluster erstellen müssen, der Beispiele oder Operatoren für Red Hat oder zertifizierte Partner beinhaltet.

    Wenn Sie den Befehl `az aro create` ausführen, können Sie mit dem Parameter `--pull-secret @pull-secret.txt` auf Ihr Pullgeheimnis verweisen. Führen Sie `az aro create` von dem Verzeichnis aus, in dem Sie Ihre `pull-secret.txt`-Datei gespeichert haben. Ersetzen Sie andernfalls `@pull-secret.txt` durch `@/path/to/my/pull-secret.txt`.

    Wenn Sie Ihr Pullgeheimnis kopieren oder es in anderen Skripts referenzieren, sollte Ihr Pullgeheimnis als gültige JSON-Zeichenfolge formatiert werden.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Vorbereiten einer benutzerdefinierten Domäne für Ihren Cluster (optional)

Wenn Sie den Befehl `az aro create` ausführen, können Sie mithilfe des `--domain foo.example.com`-Parameters eine benutzerdefinierte Domäne für den Cluster angeben.

Beachten Sie die folgenden Punkte, wenn Sie eine benutzerdefinierte Domäne für Ihren Cluster bereitstellen:

* Nachdem Sie den Cluster erstellt haben, müssen Sie im DNS-Server 2 DNS A-Einträge für die angegebene `--domain` erstellen:
    * **api**: Verweist auf die IP-Adresse des API-Servers.
    * **\*.apps**: Verweist auf die Eingangs-IP-Adresse.
    * Rufen Sie diese Werte ab, indem Sie den folgenden Befehl nach der Clustererstellung ausführen: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`.

* Die OpenShift-Konsole ist anstelle der integrierten Domäne `https://console-openshift-console.apps.<random>.<location>.aroapp.io` unter einer URL wie `https://console-openshift-console.apps.example.com` verfügbar.

* OpenShift verwendet standardmäßig selbstsignierte Zertifikate für alle in benutzerdefinierten `*.apps.example.com`-Domänen erstellten Routen.  Wenn Sie nach dem Herstellen einer Verbindung mit dem Cluster benutzerdefiniertes DNS verwenden möchten, müssen Sie anhand der OpenShift-Dokumentation [eine benutzerdefinierte Zertifizierungsstelle für Ihren Eingangscontroller](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) und eine [benutzerdefinierte Zertifizierungsstelle für Ihren API-Server](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html) konfigurieren.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Erstellen eines virtuellen Netzwerks mit zwei leeren Subnetzen

Als Nächstes erfahren Sie, wie Sie ein virtuelles Netzwerk mit zwei leeren Subnetzen erstellen. Wenn Sie über ein vorhandenes virtuelles Netzwerk verfügen, das Ihre Anforderungen erfüllt, können Sie diesen Schritt überspringen.

1. **Legen Sie die folgenden Variablen in der Shell-Umgebung fest, in der Sie die `az`-Befehle ausführen.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Erstellen Sie eine Ressourcengruppe.**

   Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Speicherort angeben. An diesem Speicherort werden die Metadaten der Ressourcengruppe gespeichert. Darüber hinaus werden dort die Ressourcen in Azure ausgeführt, wenn Sie während der Ressourcenerstellung keine andere Region angeben. Erstellen Sie mit dem Befehl [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe.
    
   > [!NOTE] 
   > Azure Red Hat Openshift ist nicht in allen Regionen verfügbar, in denen eine Azure-Ressourcengruppe erstellt werden kann. Wo Azure Red Hat Openshift unterstützt wird, erfahren Sie unter [Available Regions](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) (Verfügbare Regionen).

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Erstellen Sie ein virtuelles Netzwerk.**

   Für Azure Red Hat OpenShift-Cluster mit OpenShift 4 ist ein virtuelles Netzwerk mit zwei leeren Subnetzen für die Master- und Workerknoten erforderlich. Sie können entweder ein neues virtuelles Netzwerk dafür erstellen oder ein vorhandenes virtuelles Netzwerk verwenden.

   Erstellen Sie ein neues virtuelles Netzwerk in derselben Ressourcengruppe, die Sie zuvor erstellt haben:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   Die folgende Beispielausgabe zeigt, dass das virtuelle Netzwerk erfolgreich erstellt wurde:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Fügen Sie ein leeres Subnetz für die Masterknoten hinzu.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Fügen Sie ein leeres Subnetz für die Workerknoten hinzu.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **[Deaktivieren Sie Richtlinien für private Endpunkte](../private-link/disable-private-link-service-network-policy.md) im Mastersubnetz.** Dies ist erforderlich, damit sich der Dienst mit dem Cluster verbinden und ihn verwalten kann.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Erstellen Sie den Cluster.

Führen Sie den folgenden Befehl aus, um einen Cluster zu erstellen. Wenn Sie eine der folgenden Optionen verwenden möchten, ändern Sie den Befehl entsprechend:
* Optional können Sie Ihr [Red Hat-Pullgeheimnis übergeben](#get-a-red-hat-pull-secret-optional), das dem Cluster den Zugriff auf Red Hat-Containerregistrierungen und zusätzliche Inhalte ermöglicht. Fügen Sie dem Befehl das `--pull-secret @pull-secret.txt`-Argument hinzu.
* Optional können Sie eine [benutzerdefinierte Domäne verwenden](#prepare-a-custom-domain-for-your-cluster-optional). Fügen Sie dem Befehl das `--domain foo.example.com`-Argument hinzu, und ersetzen Sie `foo.example.com` durch Ihre eigene benutzerdefinierte Domäne.

> [!NOTE]
> Wenn Sie Ihrem Befehl optionale Argumente hinzufügen, stellen Sie sicher, dass Sie das Argument in der vorhergehenden Zeile des Befehls mit einem abschließenden umgekehrten Schrägstrich abschließen.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Nach der Ausführung des Befehls `az aro create` dauert es normalerweise etwa 35 Minuten, um einen Cluster zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Einrichten der erforderlichen Komponenten sowie Erstellen des erforderlichen virtuellen Netzwerks und der Subnetze
> * Bereitstellen eines Clusters

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster](tutorial-connect-cluster.md)
