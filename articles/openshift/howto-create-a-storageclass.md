---
title: Erstellen einer Azure Files Storage-Klasse in Azure Red Hat OpenShift 4
description: Hier erfahren Sie, wie Sie in Azure Red Hat OpenShift eine Azure Files Storage-Klasse erstellen.
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro, openshift, az aro, red hat, cli, azure files
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: db87e61166da87300a5e1fb8433a42f3f47cf832
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493823"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Erstellen einer Azure Files Storage-Klasse in Azure Red Hat OpenShift 4

In diesem Artikel wird eine Storage-Klasse für Azure Red Hat OpenShift 4 zur dynamischen Bereitstellung von RWX-Speicher (ReadWriteMany) unter Verwendung von Azure Files erstellt. Sie lernen Folgendes:

> [!div class="checklist"]
> * Einrichten der erforderlichen Komponenten und Installieren der erforderlichen Tools
> * Erstellen einer Storage-Klasse in Azure Red Hat OpenShift 4 mithilfe der Azure Files-Bereitstellung

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 2.6.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie einen Azure Red Hat OpenShift 4-Cluster in Ihrem Abonnement bereit. Eine entsprechende Anleitung finden Sie unter [Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters](tutorial-create-cluster.md).


### <a name="set-up-azure-storage-account"></a>Einrichten des Azure-Speicherkontos

In diesem Schritt wird eine Ressourcengruppe außerhalb der Ressourcengruppe des ARO-Clusters (Azure Red Hat OpenShift) erstellt. Diese Ressourcengruppe enthält die Azure Files-Freigaben, die von der dynamischen Bereitstellung von Azure Red Hat OpenShift erstellt werden.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Festlegen von Berechtigungen
### <a name="set-resource-group-permissions"></a>Festlegen der Ressourcengruppenberechtigungen

Der ARO-Dienstprinzipal benötigt die Berechtigung „listKeys“ für die neue Ressourcengruppe des Azure-Speicherkontos. Weisen Sie die Rolle „Mitwirkender“ zu, um dies zu erreichen. 

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER –-query servicePrincipalProfile.clientId -o tsv)

az role assignment create –-role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Festlegen der ARO-Clusterberechtigungen

Das OpenShift-Binderdienstkonto für persistente Volumes muss Geheimnisse lesen können. Erstellen Sie eine OpenShift-Clusterrolle, und weisen Sie sie zu, um dies zu erreichen.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Erstellen einer Storage-Klasse mit einer Azure Files-Bereitstellung

In diesem Schritt wird eine Storage-Klasse mit einer Azure Files-Bereitstellung erstellt. Im Manifest der Storage-Klasse müssen die Details des Speicherkontos enthalten sein, um den ARO-Cluster darüber zu informieren, dass nach einem Speicherkonto gesucht werden muss, das sich außerhalb der aktuellen Ressourcengruppe befindet.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS 
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Ändern der standardmäßigen Storage-Klasse (optional)

Die standardmäßige Storage-Klasse in ARO heißt „managed-premium“ und verwendet die Azure-Datenträgerbereitstellung. Ändern Sie dies, indem Sie Patchbefehle für die Manifeste der Storage-Klasse ausgeben.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Überprüfen der Azure Files Storage-Klasse (optional)

Erstellen Sie eine neue Anwendung, und weisen Sie ihr Speicher zu.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
Die Datei „test.txt“ wird auch im Azure-Portal im Storage-Explorer angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mithilfe von Microsoft Azure Files und Azure Red Hat OpenShift 4 einen dynamischen beständigen Speicher erstellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Speicherkontos
> * Konfigurieren einer Storage-Klasse in einem Azure Red Hat OpenShift 4-Cluster mithilfe der Azure Files-Bereitstellung

Im nächsten Artikel erfahren Sie mehr über die von Azure Red Hat OpenShift 4 unterstützten Ressourcen:

* [Unterstützungsrichtlinien in Azure Red Hat OpenShift](support-policies-v4.md)