---
title: Verwenden von Container Storage Interface-Treibern (CSI) für Azure Files in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie die Container Storage Interface-Treiber (CSI) für Azure Files in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: b29f4034b12ce43e6c051e454601f196365469f3
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636979"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Verwenden von Container Storage Interface-Treibern (CSI) von Azure Files in Azure Kubernetes Service (AKS) (Vorschauversion)

Der Container Storage Interface-Treiber (CSI) von Azure Files ist ein mit der [CSI-Spezifikation](https://github.com/container-storage-interface/spec/blob/master/spec.md) konformer Treiber, der von Azure Kubernetes Service (AKS) zum Verwalten des Lebenszyklus von Azure Files-Freigaben verwendet wird.

CSI ist ein Standard für die Bereitstellung beliebiger Block- und Dateispeichersysteme für containerisierte Workloads in Kubernetes. Durch die Übernahme und Verwendung von CSI kann AKS nun Plug-Ins schreiben, bereitstellen und durchlaufen, um neue oder verbesserte vorhandene Speichersysteme in Kubernetes verfügbar zu machen, ohne den Kerncode von Kubernetes zu ändern oder die Releasezyklen abwarten zu müssen.

Informationen zum Erstellen eines AKS-Clusters mit Unterstützung für CSI-Treiber finden Sie unter [Aktivieren von CSI-Treibern für Azure-Datenträger und Azure Files in AKS](csi-storage-drivers.md).

>[!NOTE]
> *Strukturinterne Treiber* sind die aktuellen Speichertreiber, die Teil des Kubernetes-Kerncodes sind, also nicht die neuen CSI-Treiber, die als Plug-Ins bereitgestellt werden.

## <a name="use-a-persistent-volume-with-azure-files"></a>Verwenden eines persistenten Volumes mit Azure Files

Ein [persistentes Volume (PV)](concepts-storage.md#persistent-volumes) stellt ein Speicherelement dar, das für die Verwendung mit Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet und dynamisch oder statisch bereitgestellt werden. Wenn mehrere Pods gleichzeitig Zugriff auf dasselbe Speichervolume benötigen, können Sie Azure Files verwenden, um mithilfe des [SMB-Protokolls (Server Message Block)][smb-overview] eine Verbindung herzustellen. In diesem Artikel wird gezeigt, wie Sie dynamisch eine Azure Files-Freigabe erstellen, die von mehreren Pods in einem AKS-Cluster verwendet wird. Informationen zur statischen Bereitstellung finden Sie unter [Manuelles Erstellen und Verwenden eines Volumes mit Azure Files-Freigabe](azure-files-volume.md).

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dynamisches Erstellen von persistenten Azure Files-Volumes mithilfe der integrierten Speicherklassen

Mit einer Speicherklasse wird festgelegt, wie eine Azure Files-Freigabe erstellt wird. In der [Knotenressourcengruppe][node-resource-group] wird automatisch ein Speicherkonto zur Verwendung mit der Speicherklasse und zur Speicherung von Azure Files-Freigaben erstellt. Wählen Sie für *skuName* eine der folgenden [Azure-Speicherredundanz-SKUs][storage-skus] aus:

* **Standard_LRS:** Standard – lokal redundanter Speicher
* **Standard_GRS**: Standard – georedundanter Speicher
* **Standard_ZRS**: Standard – zonenredundanter Speicher
* **Standard_RAGRS**: Standard – georedundanter Speicher mit Lesezugriff
* **Premium_LRS**: Premium – lokal redundanter Speicher

> [!NOTE]
> Azure Files unterstützt Azure Storage Premium. Die kleinstmögliche Premium-Dateifreigabe beträgt 100 GB.

Wenn Sie CSI-Speichertreiber in AKS verwenden, gibt es zwei zusätzliche integrierte `StorageClasses`, die CSI-Speichertreiber für Azure Files verwenden. Die zusätzlichen CSI-Speicherklassen werden zusammen mit dem Cluster und zusätzlich zu den Standardspeicherklassen in der Struktur erstellt.

- `azurefile-csi`: Verwendet Azure Storage Standard zum Erstellen einer Azure Files-Freigabe
- `azurefile-csi-premium`: Verwendet Azure Storage Premium zum Erstellen einer Azure Files-Freigabe

Die Freigaberichtlinie für beide Speicherklassen stellt sicher, dass beim Löschen eines persistenten Volumes auch die zugrunde liegende Azure Files Freigabe gelöscht wird. Mit der Speicherklasse werden auch die Dateifreigaben so konfiguriert, dass sie erweiterbar sind. Sie müssen lediglich den Anspruch auf persistente Volumes (Persistent Volume Claim, PVC) an die neue Größe anpassen.

Um diese Speicherklassen zu verwenden, erstellen Sie einen [PVC](concepts-storage.md#persistent-volume-claims) und den entsprechenden Pod, der auf diesen verweist und ihn verwendet. Ein Anspruch auf ein persistentes Volume wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen. Ein Anspruch auf ein persistentes Volume kann eine der vorab erstellten Speicherklassen oder eine benutzerdefinierte Speicherklasse verwenden, um eine Azure Files-Freigabe für die gewünschte SKU und Größe zu erstellen. Wenn Sie eine Poddefinition erstellen, wird der Anspruch auf ein persistentes Volume angegeben, um den gewünschten Speicher anzufordern.

Erstellen Sie mit dem Befehl [kubectl apply][kubectl-apply] einen [Beispielanspruch auf ein persistentes Volume, der das aktuelle Datum in ein `outfile` ausgibt](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml):

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Wenn der Pod den Status „Wird ausgeführt“ aufweist, können Sie überprüfen, ob die Dateifreigabe ordnungsgemäß eingebunden wurde, indem Sie den folgenden Befehl ausführen. Überprüfen Sie dann, ob die Ausgabe das `outfile` enthält:

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Erstellen einer benutzerdefinierten Speicherklasse

Die Standardspeicherklassen eignen sich für die gängigsten Szenarien, aber nicht für alle. In einigen Fällen möchten Sie möglicherweise Ihre eigene Speicherklasse mit eigenen Parametern anpassen. Verwenden Sie z. B. das folgende Manifest, um die `mountOptions` der Dateifreigabe zu konfigurieren.

Der Standardwert für *fileMode* und *dirMode* lautet bei in Kubernetes eingebundenen Dateifreigaben *0777*. Sie können die verschiedenen Einbindungsoptionen im Speicherklassenobjekt angeben.

Erstellen Sie eine Datei mit dem Namen `azure-file-sc.yaml`, und fügen Sie das folgende Beispielmanifest ein:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Verwenden Sie den Befehl [kubectl apply][kubectl-apply] zum Erstellen der Speicherklasse:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Der Azure Files-CSI-Treiber unterstützt das Erstellen von [Momentaufnahmen von permanenten Volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) und den zugrunde liegenden Dateifreigaben.

Verwenden Sie den Befehl [kubectl apply][kubectl-apply] zum Erstellen einer [Momentaufnahmeklasse für das Volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml):

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Erstellen Sie eine [Volumemomentaufnahme](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) mit dem Anspruch auf ein persistentes Volume, den Sie [zu Beginn dieses Tutorials dynamisch erstellt haben](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) (`pvc-azurefile`).


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Überprüfen Sie, ob die Momentaufnahme korrekt erstellt wurde:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Ändern der Größe eines persistenten Volumes

Sie können ein größeres Volume für einen Anspruch auf ein persistentes Volume anfordern. Bearbeiten Sie das PVC-Objekt, und geben Sie eine höhere Größe an. Diese Änderung löst die Erweiterung des zugrunde liegenden Volumes für den Anspruch auf ein persistentes Volume aus.

> [!NOTE]
> Für den Anspruch wird nie ein neues persistentes Volume erstellt. Stattdessen wird die Größe eines vorhandenen Volumes geändert.

In AKS unterstützt die integrierte Speicherklasse `azurefile-csi` bereits Erweiterungen. Verwenden Sie daher [den zuvor erstellten Anspruch auf ein persistentes Volume mit dieser Speicherklasse](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Der Anspruch auf ein persistentes Volume hat eine 100 GiB große Dateifreigabe angefordert. Sie können dies überprüfen, indem Sie Folgendes ausführen:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Vergrößern Sie das Feld `spec.resources.requests.storage`, um den Anspruch auf ein persistentes Volume zu erweitern:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Überprüfen Sie, ob sowohl der Anspruch auf ein persistentes Volume als auch das Dateisystem im Pod die neue Größe aufweisen:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-Dateifreigaben
[Azure Files unterstützt jetzt das NFS v4.1-Protokoll](../storage/files/storage-files-how-to-create-nfs-shares.md). Die NFS 4.1-Unterstützung in Azure Files bietet ein vollständig verwaltetes NFS-Dateisystem als Dienst, der auf einer hochverfügbaren, extrem robusten, verteilten und resilienten Speicherplattform basiert.

 Diese Option ist für Workloads mit zufälligem Zugriff und direkten Datenaktualisierungen optimiert und bietet vollständige Unterstützung für POSIX-Dateisysteme. In diesem Abschnitt erfahren Sie, wie Sie NFS-Freigaben mit dem Azure Files-CSI-Treiber in einem AKS-Cluster verwenden.

Beachten Sie unbedingt die [Einschränkungen](../storage/files/storage-files-compare-protocols.md#limitations) und die [regionale Verfügbarkeit](../storage/files/storage-files-compare-protocols.md#regional-availability) während der Vorschauphase.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrieren der Previewfunktion `AllowNfsFileShares`

Wenn Sie eine Dateifreigabe erstellen möchten, die NFS 4.1 nutzt, müssen Sie das Featureflag `AllowNfsFileShares` in Ihrem Abonnement aktivieren.

Registrieren Sie das Featureflag `AllowNfsFileShares` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des Ressourcenanbieters *Microsoft.Storage* mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Erstellen eines Speicherkontos für die NFS-Dateifreigabe

[Erstellen Sie ein Azure Storage-Konto vom Typ `Premium_LRS`](../storage/files/storage-how-to-create-premium-fileshare.md) mit den folgenden Konfigurationen, um NFS-Freigaben zu unterstützen:
- Kontotyp: FileStorage
- Sichere Übertragung erforderlich (nur HTTPS-Datenverkehr aktivieren): FALSE
- Wählen Sie das virtuelle Netzwerk der Agent-Knoten in Firewalls und virtuellen Netzwerken aus. Daher sollten Sie das Speicherkonto in der MC_-Ressourcengruppe erstellen.

### <a name="create-nfs-file-share-storage-class"></a>Erstellen einer Speicherklasse für die NFS-Dateifreigabe

Speichern Sie eine Datei `nfs-sc.yaml` mit dem unten gezeigten Manifest, und bearbeiten Sie die entsprechenden Platzhalter.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Erstellen Sie nach dem Bearbeiten und Speichern der Datei die Speicherklasse mit dem Befehl [kubectl apply][kubectl-apply]:

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Erstellen einer Bereitstellung mit einer NFS-gestützten Dateifreigabe
Sie können einen [zustandsbehafteten Beispielsatz](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) bereitstellen, mit dem Zeitstempel in einer Datei `data.txt` gespeichert werden, indem Sie den folgenden Befehl mit dem Befehl [kubectl apply][kubectl-apply] bereitstellen:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Überprüfen Sie den Inhalt des Volumes, indem Sie Folgendes ausführen:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Beachten Sie Folgendes: Da die NFS-Dateifreigabe ein Premium-Konto ist, beträgt die Mindestgröße für die Dateifreigabe 100 GB. Wenn Sie einen PVC mit einer kleinen Speichergröße erstellen, tritt möglicherweise ein Fehler auf: „Fehler beim Erstellen der Dateifreigabe... Größe (5)...“.


## <a name="windows-containers"></a>Windows-Container

Der Azure Files-CSI-Treiber unterstützt auch Windows-Knoten und -Container. Wenn Sie Windows-Container verwenden möchten, befolgen Sie das [Tutorial zu Windows-Containern](windows-container-cli.md), um einen Windows-Knotenpool hinzuzufügen.

Wenn Sie über einen Windows-Knotenpool verfügen, können Sie die integrierten Speicherklassen wie `azurefile-csi` verwenden oder benutzerdefinierte Speicherklassen erstellen. Sie können einen [Windows-basierten zustandsbehafteten Beispielsatz](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) bereitstellen, mit dem Zeitstempel in einer Datei `data.txt` gespeichert werden, indem Sie den folgenden Befehl mit dem Befehl [kubectl apply][kubectl-apply] bereitstellen:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Überprüfen Sie den Inhalt des Volumes, indem Sie Folgendes ausführen:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von CSI-Treibern für Azure-Datenträger finden Sie unter [Verwenden von Azure-Datenträgern mit CSI-Treibern](azure-disk-csi.md).
- Weitere Informationen zu bewährten Methoden bei der Speicherung finden Sie unter [Best Practices für Speicherung und Sicherungen in Azure Kubernetes Service][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
