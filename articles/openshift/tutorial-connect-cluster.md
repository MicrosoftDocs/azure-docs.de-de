---
title: 'Tutorial: Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster'
description: Hier erfahren Sie, wie Sie eine Verbindung mit einem Microsoft Azure Red Hat OpenShift-Cluster herstellen.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 31abf5008c082fbb54e873b0502876c2fce5d440
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635964"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Herstellen einer Verbindung mit einem Azure Red Hat OpenShift 4-Cluster

In diesem Tutorial (Teil 2 von 3) stellen Sie als kubeadmin-Benutzer über die OpenShift-Webkonsole eine Verbindung mit einem Azure Red Hat OpenShift-Cluster mit OpenShift 4 her. Folgendes wird vermittelt:
> [!div class="checklist"]
> * Abrufen von `kubeadmin`-Anmeldeinformationen für Ihren Cluster
> * Installieren der OpenShift-CLI
> * Herstellen einer Verbindung mit einem Azure Red Hat OpenShift-Cluster mithilfe der OpenShift-Befehlszeilenschnittstelle

## <a name="before-you-begin"></a>Voraussetzungen

In den vorherigen Tutorials wurde ein Azure Red Hat OpenShift-Cluster erstellt. Falls Sie diese Schritte noch nicht ausgeführt haben und dies jetzt nachholen möchten, sollten Sie mit [Tutorial 1: Erstellen eines Azure Red Hat OpenShift 4-Clusters](tutorial-create-cluster.md) beginnen.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 2.6.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Sie können sich mit dem Benutzer `kubeadmin` beim Cluster anmelden.  Führen Sie den folgenden Befehl aus, um das Kennwort für den Benutzer `kubeadmin` zu suchen:

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Die folgende Beispielausgabe zeigt das Kennwort in `kubeadminPassword`.

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

Starten Sie die Konsolen-URL in einem Browser, und melden Sie sich mit den `kubeadmin`-Anmeldeinformationen an.

![Azure Red Hat OpenShift-Anmeldebildschirm](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installieren der OpenShift-CLI

Wenn Sie bei der OpenShift-Webkonsole angemeldet sind, klicken Sie auf das Fragezeichen ( **?** ) im oberen rechten Bereich und dann auf **Befehlszeilentools**. Laden Sie die für Ihren Computer geeignete Version herunter.

![Der Screenshot, der die Option „Befehlszeilentools“ in der Liste hervorhebt, wenn Sie das Symbol „?“ auswählen.](media/aro4-download-cli.png)

Sie können auch unter <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> die aktuelle Version der CLI herunterladen, die für Ihren Computer geeignet ist.

Wenn Sie die Befehle in Azure Cloud Shell ausführen, laden Sie die aktuelle OpenShift 4-Befehlszeilenschnittstelle für Linux herunter.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Herstellen einer Verbindung mithilfe der OpenShift-Befehlszeilenschnittstelle

Rufen Sie die Adresse des API-Servers ab:

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Melden Sie sich mit dem folgenden Befehl beim API-Server des OpenShift-Clusters an. Ersetzen Sie **\<kubeadmin password>** durch das soeben abgerufene Kennwort.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Abrufen von `kubeadmin`-Anmeldeinformationen für Ihren Cluster
> * Installieren der OpenShift-CLI
> * Herstellen einer Verbindung mit einem Azure Red Hat OpenShift-Cluster mithilfe der OpenShift-Befehlszeilenschnittstelle

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Löschen eines Azure Red Hat OpenShift-Clusters](tutorial-delete-cluster.md)