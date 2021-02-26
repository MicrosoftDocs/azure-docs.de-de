---
title: Verwenden von Azure Container Registry mit Azure Red Hat OpenShift
description: Hier erfahren Sie, wie Sie einen Container aus Azure Container Registry in Ihren Azure Red Hat OpenShift-Cluster pullen und ausführen.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064351"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Verwenden von Azure Container Registry mit Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) ist ein verwalteter Containerregistrierungsdienst, mit dem Sie private Docker-Containerimages mit Unternehmensfunktionen wie der Georeplikation speichern können. Für den Zugriff auf ACR von einem ARO-Cluster kann sich der Cluster bei ACR authentifizieren, indem Docker-Anmeldeinformationen in einem Kubernetes-Geheimnis gespeichert werden.  Ebenso kann ein ARO-Cluster „imagePullSecret“ in der Podspezifikation verwenden, um sich beim Pullen des Images bei der Registrierung zu authentifizieren. In diesem Artikel erfahren Sie, wie Sie Azure Container Registry mit einem Azure Red Hat OpenShift-Cluster einrichten, um private Docker-Containerimages zu speichern und zu pullen.

## <a name="prerequisites"></a>Voraussetzungen

Bei dieser Anleitung wird davon ausgegangen, dass Sie über eine vorhandene Azure Container Registry-Instanz verfügen. Wenn das nicht der Fall ist, verwenden Sie das Azure-Portal oder die [Anweisungen für die Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), um eine Containerregistrierung zu erstellen.

In diesem Artikel wird außerdem davon ausgegangen, dass Sie über einen vorhandenen Azure Red Hat OpenShift-Cluster verfügen und die `oc`-CLI installiert haben. Falls nicht, folgen Sie den Anweisungen im [Tutorial zum Erstellen eines ARO-Clusters](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Abrufen eines Pullgeheimnisses

Für den Zugriff auf die Registrierung von Ihrem ARO-Cluster benötigen Sie ein Pullgeheimnis von ACR.

Sie können entweder das Azure-Portal oder die Azure CLI zum Abrufen der Anmeldeinformationen für das Pullgeheimnis verwenden.

Wenn Sie das Azure-Portal verwenden, navigieren Sie zu Ihrer ACR-Instanz, und wählen Sie **Zugriffsschlüssel** aus.  Ihr `docker-username` ist der Name Ihrer Containerregistrierung. Verwenden Sie entweder „password“ oder „password2“ für `docker-password`.

![Zugriffsschlüssel](./media/acr-access-keys.png)

Sie können stattdessen auch die Azure CLI verwenden, um diese Anmeldeinformationen abzurufen:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Erstellen des Kubernetes-Geheimnisses

Nun werden diese Anmeldeinformationen zum Erstellen eines Kubernetes-Geheimnisses verwendet. Führen Sie den folgenden Befehl mit Ihren ACR-Anmeldeinformationen aus:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Dieses Geheimnis wird im aktuellen OpenShift-Projekt (Kubernetes-Namespace) gespeichert, und kann nur von Pods referenziert werden, die in diesem Projekt erstellt wurden.  Weitere Anweisungen zum Erstellen eines clusterweiten Pullgeheimnisses finden Sie in diesem [Dokument](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html).

## <a name="create-a-pod-using-a-private-registry-image"></a>Erstellen eines Pods mithilfe eines privaten Registrierungsimages

Nachdem Ihr ARO-Cluster mit Ihrer ACR-Instanz verbunden wurde, können Sie ein Image aus Ihrer ACR pullen, um einen Pod zu erstellen.

Beginnen Sie mit einer PodSpec, und geben Sie das als „imagePullSecret“ erstellte Geheimnis an:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Um zu testen, ob der Pod ausgeführt wird, verwenden Sie den folgenden Befehl, und warten Sie, bis der Status **Running** (Wird ausgeführt) lautet:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Nächste Schritte

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
