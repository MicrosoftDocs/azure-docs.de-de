---
title: Erstellen eines Datencontrollers mithilfe von [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
description: Erstellen Sie einen Azure Arc-Datencontroller in einem typischen Kubernetes-Cluster mit mehreren Knoten, den Sie bereits mithilfe von [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] erstellt haben.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 94f347cc24c675c69c69dad6a7d7a796b395c1a6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493611"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Erstellen eines Azure Arc-Datencontrollers mithilfe von [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die Übersicht im Thema [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md).

Zum Erstellen des Azure Arc-Datencontrollers mithilfe der [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] muss die [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installiert sein.

   [Installieren der [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Unabhängig von der ausgewählten Zielplattform müssen Sie vor der Erstellung die folgenden Umgebungsvariablen für den Datencontrolleradministrator festlegen. Sie können diese Anmeldeinformationen anderen Benutzern mitteilen, die ggf. Administratorzugriff auf den Datencontroller benötigen.

**AZDATA_USERNAME**: Ein Benutzername Ihrer Wahl für den Datencontrolleradministrator. Beispiel: `arcadmin`

**AZDATA_PASSWORD**: Ein Kennwort Ihrer Wahl für den Datencontrolleradministrator. Das Kennwort muss mindestens acht Zeichen enthalten, die aus drei der folgenden vier Kategorien stammen: Großbuchstaben, Kleinbuchstaben, Ziffern und Symbole.

### <a name="linux-or-macos"></a>Linux oder macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Sie müssen eine Verbindung mit einem Kubernetes-Cluster herstellen und sich authentifizieren sowie einen vorhandenen Kubernetes-Kontext auswählen, bevor Sie mit der Erstellung des Azure Arc-Datencontrollers beginnen. Wie Sie eine Verbindung mit einem Kubernetes-Cluster oder -Dienst herstellen, ist von Fall zu Fall unterschiedlich. Informieren Sie sich in der Dokumentation zur verwendeten Kubernetes-Distribution oder zum verwendeten Kubernetes-Dienst darüber, wie eine Verbindung mit dem Kubernetes-API-Server hergestellt wird.

Mit den folgenden Befehlen können Sie überprüfen, ob Sie über eine aktuelle Kubernetes-Verbindung verfügen, und den aktuellen Kontext bestätigen.

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Erstellen des Azure Arc-Datencontrollers

> [!NOTE]
> In den folgenden Beispielen können Sie verschiedene Werte für den Parameter `--namespace` des Befehls „azdata arc dc create“ verwenden. Achten Sie jedoch darauf, dass Sie diesen Namespacenamen für `--namespace parameter` in allen anderen unten aufgeführten Befehlen verwenden.

Befolgen Sie je nach Zielplattform den entsprechenden Abschnitt unten, um den Erstellungsvorgang zu konfigurieren.

[Erstellen im Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)

[Erstellen in der AKS-Engine in Azure Stack Hub](#create-on-aks-engine-on-azure-stack-hub)

[Erstellen in AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)

[Erstellen in Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)

[Erstellen auf der Red Hat OpenShift Container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)

[Erstellen in der Open-Source-Upstreamversion von Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[Erstellen im AWS Elastic Kubernetes Service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)

[Erstellen im Google Cloud Kubernetes Engine Service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Erstellen im Azure Kubernetes Service (AKS)

Das AKS-Bereitstellungsprofil nutzt standardmäßig die Speicherklasse `managed-premium`. Die Speicherklasse `managed-premium` funktioniert nur, wenn Sie über VMs verfügen, die mithilfe von VM-Images mit Premium-Datenträgern bereitgestellt wurden.

Wenn Sie `managed-premium` als Speicherklasse verwenden möchten, können Sie den folgenden Befehl ausführen, um den Datencontroller zu erstellen. Ersetzen Sie die Platzhalter im Befehl durch den Namen Ihrer Ressourcengruppe, die Abonnement-ID und den Azure-Standort.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wenn Sie nicht sicher sind, welche Speicherklasse Sie verwenden sollen, verwenden Sie die Speicherklasse `default`, die unabhängig vom verwendeten VM-Typ unterstützt wird. Eine Einschränkung besteht jedoch darin, dass sie nicht die höchste Leistung bietet.

Wenn Sie die Speicherklasse `default` verwenden möchten, können Sie den folgenden Befehl ausführen:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Erstellen in der AKS-Engine in Azure Stack Hub

Das Bereitstellungsprofil nutzt standardmäßig die Speicherklasse `managed-premium`. Die Speicherklasse `managed-premium` funktioniert nur, wenn Sie über Worker-VMs verfügen, die mithilfe von VM-Images mit Premium-Datenträgern in Azure Stack Hub bereitgestellt wurden.

Sie können den folgenden Befehl ausführen, um den Datencontroller unter Verwendung der Speicherklasse „managed-premium“ zu erstellen.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wenn Sie nicht sicher sind, welche Speicherklasse Sie verwenden sollen, verwenden Sie die Speicherklasse `default`, die unabhängig vom verwendeten VM-Typ unterstützt wird. In Azure Stack Hub werden Premium-Datenträger und Standard-Datenträger von derselben Speicherinfrastruktur in unterstützt. Daher wird erwartet, dass sie die gleiche allgemeine Leistung, jedoch mit unterschiedlichen IOPS-Grenzwerten bieten.

Wenn Sie die Speicherklasse `default` verwenden möchten, können Sie den folgenden Befehl ausführen.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-aks-on-azure-stack-hci"></a>Erstellen in AKS in Azure Stack HCI

Das Bereitstellungsprofil nutzt standardmäßig die Speicherklasse mit dem Namen `default` und den Diensttyp `LoadBalancer`.

Sie können den folgenden Befehl ausführen, um den Datencontroller unter Verwendung der Speicherklasse `default` und des Diensttyps `LoadBalancer` zu erstellen.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.


### <a name="create-on-azure-red-hat-openshift-aro"></a>Erstellen in Azure Red Hat OpenShift (ARO)

Um einen Datencontroller in Azure Red Hat OpenShift zu erstellen, müssen Sie die folgenden Befehle für Ihren Cluster ausführen, um die Sicherheitseinschränkungen zu lockern. Dies ist eine vorübergehende Anforderung, die in Zukunft aufgehoben wird.
> [!NOTE]
>   Verwenden Sie hier und im Befehl `azdata arc dc create` unten denselben Namespace. Beispiel: `arc`.

Zunächst müssen Sie die benutzerdefinierte Sicherheitskontexteinschränkung (Security Context Constraint, SCC) von [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) herunterladen und auf Ihren Cluster anwenden.

Führen Sie den folgenden Befehl zum Erstellen des Datencontrollers aus.
> [!NOTE]
>   Verwenden Sie hier und in den Befehlen `oc adm policy add-scc-to-user` oben denselben Namespace. Beispiel: `arc`.

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Erstellen auf der Red Hat OpenShift Container Platform (OCP)


> [!NOTE]
> Bei Verwendung der Red Hat OpenShift Container Platform in Azure empfiehlt es sich, die neueste verfügbare Version zu verwenden.

Um einen Datencontroller auf der Red Hat OpenShift Container Platform zu erstellen, müssen Sie die folgenden Befehle für Ihren Cluster ausführen, um die Sicherheitseinschränkungen zu lockern. Dies ist eine vorübergehende Anforderung, die in Zukunft aufgehoben wird.
> [!NOTE]
>   Verwenden Sie hier und im Befehl `azdata arc dc create` unten denselben Namespace. Beispiel: `arc`.

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

Sie müssen außerdem bestimmen, welche Speicherklasse verwendet werden soll, indem Sie den folgenden Befehl ausführen.

```console
kubectl get storageclass
```

Beginnen Sie zunächst mit dem Erstellen einer neuen benutzerdefinierten Datei für das Bereitstellungsprofil auf Grundlage des Bereitstellungsprofils „azure-arc-openshift“, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden das Verzeichnis `custom` im aktuellen Arbeitsverzeichnis und die benutzerdefinierte Bereitstellungsprofildatei `control.json` in diesem Verzeichnis erstellt.

Verwenden Sie das Profil `azure-arc-openshift` für die OpenShift Container Platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
Verwenden Sie das Profil `azure-arc-azure-openshift` für Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

Legen Sie nun die gewünschte Speicherklasse fest, indem Sie `<storageclassname>` im folgenden Befehl durch den Namen der gewünschten Speicherklasse ersetzen, die Sie mithilfe des oben angegebenen Befehls `kubectl get storageclass` ermittelt haben.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Das Bereitstellungsprofil „azure-arc-openshift“ nutzt standardmäßig `NodePort` als Diensttyp. Wenn Sie einen OpenShift-Cluster mit integriertem Lastenausgleich verwenden, können Sie die Konfiguration mithilfe des folgenden Befehls so ändern, dass der Diensttyp „LoadBalancer“ verwendet wird:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Bei Verwendung von OpenShift kann es vorkommen, dass Sie die Umgebung mit den standardmäßigen Sicherheitsrichtlinien in OpenShift ausführen möchten oder prinzipiell stärker als üblich einschränken möchten. Sie können optional einige Funktionen deaktivieren, um die zur Bereitstellungszeit und zur Laufzeit erforderlichen Berechtigungen zu minimieren, indem Sie die folgenden Befehle ausführen.

Dieser Befehl deaktiviert die Erfassung von Metriken zu Pods. Wenn Sie diese Funktion deaktivieren, werden in den Grafana-Dashboards keine Metriken zu Pods angezeigt. Der Standardwert ist "True".

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Dieser Befehl deaktiviert die Erfassung von Metriken zu Knoten. Wenn Sie diese Funktion deaktivieren, werden in den Grafana-Dashboards keine Metriken zu Knoten angezeigt. Der Standardwert ist "True".

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Mit diesem Befehl wird die Möglichkeit deaktiviert, Speicherabbilder zur Problembehandlung zu erstellen.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

Nun können Sie den Datencontroller mit dem folgenden Befehl erstellen.
> [!NOTE]
>   Verwenden Sie hier und in den Befehlen `oc adm policy add-scc-to-user` oben denselben Namespace. Beispiel: `arc`.

> [!NOTE]
>   Der Parameter `--path` muss auf das _Verzeichnis_ mit der Datei „control.json“ und nicht auf die Datei „control.json“ selbst verweisen.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Erstellen in der Open-Source-Upstreamversion von Kubernetes (kubeadm)

Das kubeadm-Bereitstellungsprofil nutzt standardmäßig eine Speicherklasse mit dem Namen `local-storage` und den Diensttyp `NodePort`. Wenn dies akzeptabel ist, können Sie die nachfolgenden Anweisungen überspringen, mit deren Hilfe die gewünschte Speicherklasse und der Diensttyp festgelegt werden, und den Befehl `azdata arc dc create` unten direkt ausführen.

Wenn Sie Ihr Bereitstellungsprofil anpassen möchten, um eine bestimmte Speicherklasse und/oder einen bestimmten Diensttyp anzugeben, erstellen Sie zunächst eine neue benutzerdefinierte Bereitstellungsprofildatei auf Grundlage des kubeadm-Bereitstellungsprofils, indem Sie den folgenden Befehl ausführen. Mit diesem Befehl werden das Verzeichnis `custom` im aktuellen Arbeitsverzeichnis und die benutzerdefinierte Bereitstellungsprofildatei `control.json` in diesem Verzeichnis erstellt.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Sie können die verfügbaren Speicherklassen mithilfe des folgenden Befehls ermitteln.

```console
kubectl get storageclass
```

Legen Sie nun die gewünschte Speicherklasse fest, indem Sie `<storageclassname>` im folgenden Befehl durch den Namen der gewünschten Speicherklasse ersetzen, die Sie mithilfe des oben angegebenen Befehls `kubectl get storageclass` ermittelt haben.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Das kubeadm-Bereitstellungsprofil nutzt standardmäßig `NodePort` als Diensttyp. Wenn Sie einen Kubernetes-Cluster mit integriertem Lastenausgleich verwenden, können Sie die Konfiguration mithilfe des folgenden Befehls ändern.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Nun können Sie den Datencontroller mit dem folgenden Befehl erstellen.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Erstellen im AWS Elastic Kubernetes Service (EKS)

Standardmäßig lauten die EKS-Speicherklasse `gp2` und der Diensttyp `LoadBalancer`.

Führen Sie den folgenden Befehl aus, um den Datencontroller mit dem bereitgestellten EKS-Bereitstellungsprofil zu erstellen.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Erstellen im Google Cloud Kubernetes Engine Service (GKE)

Standardmäßig lauten die GKE-Speicherklasse `standard` und der Diensttyp `LoadBalancer`.

Führen Sie den folgenden Befehl aus, um den Datencontroller mit dem bereitgestellten GKE-Bereitstellungsprofil zu erstellen.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Nachdem Sie den Befehl ausgeführt haben, fahren Sie mit [Überwachen des Erstellungsstatus](#monitoring-the-creation-status) fort.

## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Das Erstellen des Controllers dauert einige Minuten. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Bei den folgenden Beispielbefehlen wird davon ausgegangen, dass Sie einen Datencontroller und Kubernetes-Namespace mit dem Namen `arc` erstellt haben. Wenn Sie einen anderen Namespace-/Datencontrollernamen verwendet haben, können Sie `arc` durch diesen Namen ersetzen.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie einen Befehl wie den folgenden ausführen. Dies ist besonders bei der Problembehandlung hilfreich.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).
