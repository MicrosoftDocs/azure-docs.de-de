---
title: Erstellen eines privaten Azure Red Hat OpenShift 4-Clusters
description: Informationen zum Erstellen eines privaten Azure Red Hat OpenShift Clusters mit OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: georgewallace
ms.author: gwallace
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3864d48399f00d5cfbdfa0a94939be0d88a73322
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928058"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Erstellen eines privaten Azure Red Hat OpenShift 4-Clusters

In diesem Artikel erfahren Sie, wie Sie Ihre Umgebung für das Erstellen von privaten Azure Red Hat OpenShift-Clustern mit OpenShift 4 vorbereiten. Sie lernen Folgendes:

> [!div class="checklist"]
> * Einrichten der erforderlichen Komponenten sowie Erstellen des erforderlichen virtuellen Netzwerks und der Subnetze
> * Bereitstellen eines Clusters mit einem privaten Endpunkt für den API-Server und einem privaten Eingangscontroller

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial Version 2.6.0 oder höher der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voraussetzungen

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

1. **[Navigieren Sie zum Manager-Portal für Ihren Red Hat OpenShift-Cluster](https://cloud.redhat.com/openshift/install/azure/aro-provisioned), und melden Sie sich an.**

   Sie müssen sich bei Ihrem Red Hat-Konto anmelden oder ein neues Red Hat-Konto mit Ihrer Geschäfts-E-Mail-Adresse erstellen und den Geschäftsbedingungen zustimmen.

2. **Klicken Sie auf „Download pull secret“ (Pullgeheimnis herunterladen).**

Speichern Sie die Datei `pull-secret.txt` an einem sicheren Ort. Sie wird bei jeder Clustererstellung verwendet.

Wenn Sie den Befehl `az aro create` ausführen, können Sie mit dem Parameter `--pull-secret @pull-secret.txt` auf Ihr Pullgeheimnis verweisen. Führen Sie `az aro create` von dem Verzeichnis aus, in dem Sie Ihre `pull-secret.txt`-Datei gespeichert haben. Ersetzen Sie andernfalls `@pull-secret.txt` durch `@<path-to-my-pull-secret-file`.

Wenn Sie Ihr Pullgeheimnis kopieren oder es in anderen Skripts referenzieren, sollte Ihr Pullgeheimnis als gültige JSON-Zeichenfolge formatiert werden.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Erstellen eines virtuellen Netzwerks mit zwei leeren Subnetzen

Als Nächstes erfahren Sie, wie Sie ein virtuelles Netzwerk mit zwei leeren Subnetzen erstellen.

1. **Legen Sie die folgenden Variablen fest:**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Erstellen einer Ressourcengruppe**

    Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Speicherort angeben. An diesem Speicherort werden die Metadaten der Ressourcengruppe gespeichert. Darüber hinaus werden dort die Ressourcen in Azure ausgeführt, wenn Sie während der Ressourcenerstellung keine andere Region angeben. Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe.

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Erstellen Sie ein virtuelles Netzwerk.**

    Für Azure Red Hat OpenShift-Cluster mit OpenShift 4 ist ein virtuelles Netzwerk mit zwei leeren Subnetzen für die Master- und Workerknoten erforderlich.

    Erstellen Sie ein neues virtuelles Netzwerk in derselben Ressourcengruppe, die Sie zuvor erstellt haben.

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

5. **[Deaktivieren Sie Richtlinien für private Endpunkte](../private-link/disable-private-link-service-network-policy.md) im Mastersubnetz.** Dies ist erforderlich, um eine Verbindung mit dem Cluster herzustellen und um diesen zu verwalten.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Erstellen Sie den Cluster.

Führen Sie den folgenden Befehl aus, um einen Cluster zu erstellen. Optional können Sie Ihr [Red Hat-Pullgeheimnis übergeben](#get-a-red-hat-pull-secret-optional), das dem Cluster den Zugriff auf Red Hat-Containerregistrierungen und zusätzliche Inhalte ermöglicht.

>[!NOTE]
> Wenn Sie Befehle kopieren/einfügen und einen der optionalen Parameter verwenden, stellen Sie sicher, dass Sie die anfänglichen Hashtags und den abschließenden Kommentartext löschen. Schließen Sie auch das Argument auf der vorhergehenden Zeile des Befehls mit einem abschließenden umgekehrten Schrägstrich ab.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Nach der Ausführung des Befehls `az aro create` dauert es normalerweise etwa 35 Minuten, um einen Cluster zu erstellen.

>[!IMPORTANT]
> Wenn Sie eine benutzerdefinierte Domäne angeben möchten (etwa **foo.example.com** ), steht die OpenShift-Konsole unter einer URL wie `https://console-openshift-console.apps.foo.example.com` anstatt über die integrierte Domäne `https://console-openshift-console.apps.<random>.<location>.aroapp.io` zur Verfügung.
>
> OpenShift verwendet standardmäßig selbstsignierte Zertifikate für alle für `*.apps.<random>.<location>.aroapp.io` erstellten Routen.  Wenn Sie nach dem Herstellen einer Verbindung mit dem Cluster ein benutzerdefiniertes DNS verwenden möchten, müssen Sie anhand der OpenShift-Dokumentation [eine benutzerdefinierte Zertifizierungsstelle für Ihren Eingangscontroller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) und eine [benutzerdefinierte Zertifizierungsstelle für Ihren API-Server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html) konfigurieren.

## <a name="connect-to-the-private-cluster"></a>Herstellen einer Verbindung mit dem privaten Cluster

Sie können sich mit dem Benutzer `kubeadmin` beim Cluster anmelden.  Führen Sie den folgenden Befehl aus, um das Kennwort für den Benutzer `kubeadmin` zu suchen:

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Die folgende Beispielausgabe zeigt, dass sich das Kennwort in `kubeadminPassword` befindet.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Sie können die URL der Clusterkonsole über den folgenden Befehl abrufen. Diese sieht in etwa wie folgt aus: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`.

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Zum Herstellen einer Verbindung mit einem Azure Red Hat OpenShift-Cluster müssen Sie den folgenden Schritt über einen Host ausführen, der sich entweder in dem von Ihnen erstellten virtuellen Netzwerk oder in einem virtuellen Netzwerk befindet, das mit dem virtuellen Netzwerk, für das der Cluster bereitgestellt wurde, per [Peering](../virtual-network/virtual-network-peering-overview.md) verknüpft ist.

Starten Sie die Konsolen-URL in einem Browser, und melden Sie sich mit den `kubeadmin`-Anmeldeinformationen an.

![Screenshot, der den Anmeldebildschirm von Azure Red Hat OpenShift zeigt.](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installieren der OpenShift-CLI

Wenn Sie bei der OpenShift-Webkonsole angemeldet sind, klicken Sie auf das Fragezeichen ( **?** ) im oberen rechten Bereich und dann auf **Befehlszeilentools**. Laden Sie die für Ihren Computer geeignete Version herunter.

![Azure Red Hat OpenShift-Anmeldebildschirm](media/aro4-download-cli.png)

Sie können auch unter <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> die aktuelle Version der CLI herunterladen, die für Ihren Computer geeignet ist.

## <a name="connect-using-the-openshift-cli"></a>Herstellen einer Verbindung mithilfe der OpenShift-CLI

Rufen Sie die Adresse des API-Servers ab.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Zum Herstellen einer Verbindung mit einem Azure Red Hat OpenShift-Cluster müssen Sie den folgenden Schritt über einen Host ausführen, der sich entweder in dem von Ihnen erstellten virtuellen Netzwerk oder in einem virtuellen Netzwerk befindet, das mit dem virtuellen Netzwerk, für das der Cluster bereitgestellt wurde, per [Peering](../virtual-network/virtual-network-peering-overview.md) verknüpft ist.

Melden Sie sich mit dem folgenden Befehl beim API-Server des OpenShift-Clusters an. Ersetzen Sie **\<kubeadmin password>** durch das soeben abgerufene Kennwort.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Sie ein Azure Red Hat OpenShift-Cluster mit OpenShift 4 bereitstellen können. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten der erforderlichen Komponenten sowie Erstellen des erforderlichen virtuellen Netzwerks und der Subnetze
> * Bereitstellen eines Clusters
> * Herstellen einer Verbindung mit dem Cluster mithilfe des `kubeadmin`-Benutzers

Im nächsten Artikel erfahren Sie, wie Sie den Cluster für die Authentifizierung mithilfe von Azure Active Directory konfigurieren können.


* [Konfigurieren der Authentifizierung mit Azure Active Directory über die Befehlszeile](configure-azure-ad-cli.md)


* [Konfigurieren der Authentifizierung mit Azure Active Directory über das Azure-Portal und die OpenShift-Webkonsole](configure-azure-ad-cli.md)
