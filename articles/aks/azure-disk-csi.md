---
title: Verwenden von Container Storage Interface-Treibern (CSI) für Azure-Datenträger in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie die Container Storage Interface-Treiber (CSI) für Azure-Datenträger in einem AKS-Cluster (Azure Kubernetes Service) verwenden.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2dba9fbcbddbc7a66763636986f3d98f4f95332c
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683130"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Verwenden von Container Storage Interface-Treibern (CSI) von Azure-Datenträgern in Azure Kubernetes Service (AKS) (Vorschauversion)
Der Container Storage Interface-Treiber (CSI) von Azure-Datenträgern ist ein mit der [CSI-Spezifikation](https://github.com/container-storage-interface/spec/blob/master/spec.md) konformer Treiber, der von Azure Kubernetes Service (AKS) zum Verwalten des Lebenszyklus von Azure-Datenträgern verwendet wird.

CSI ist ein Standard für die Bereitstellung beliebiger Block- und Dateispeichersysteme für containerisierte Workloads in Kubernetes. Durch die Übernahme und Verwendung von CSI kann AKS Plug-Ins schreiben, bereitstellen und durchlaufen, um neue oder verbesserte vorhandene Speichersysteme in Kubernetes verfügbar zu machen, ohne den Kerncode von Kubernetes zu ändern oder die Releasezyklen abwarten zu müssen.

Informationen zum Erstellen eines AKS-Clusters mit Unterstützung für CSI-Treiber finden Sie unter [Aktivieren von CSI-Treibern für Azure-Datenträger und Azure Files in AKS](csi-storage-drivers.md).

>[!NOTE]
> *Strukturinterne Treiber* bezieht sich auf die aktuellen Speichertreiber, die Teil des Kubernetes-Kerncodes sind (im Gegensatz zu den neuen CSI-Treibern, bei denen es sich um Plug-Ins handelt).

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Verwenden persistenter CSI-Volumes mit Azure-Datenträgern

Ein [persistentes Volume (PV)](concepts-storage.md#persistent-volumes) stellt ein Speicherelement dar, das für die Verwendung mit Kubernetes-Pods bereitgestellt wurde. Ein persistentes Volume kann von einem oder mehreren Pods verwendet und dynamisch oder statisch bereitgestellt werden. In diesem Artikel erfahren Sie, wie Sie dynamisch PVs mit Azure-Datenträgern zur Verwendung durch einen einzelnen Pod in einem AKS-Cluster erstellen können. Informationen zur statischen Bereitstellung finden Sie unter [Manuelles Erstellen und Verwenden eines Volumes mit Azure-Datenträgern](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Speicheroptionen für Anwendungen in AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamisches Erstellen von persistenten Azure-Datenträgervolumes mithilfe der integrierten Speicherklassen

Mit einer Speicherklasse wird festgelegt, wie eine Speichereinheit dynamisch in einem persistenten Volume erstellt wird. Weitere Informationen zu Kubernetes-Speicherklassen finden Sie unter [Kubernetes-Speicherklassen][kubernetes-storage-classes]. Wenn Sie CSI-Speichertreiber in AKS verwenden, gibt es zwei zusätzliche integrierte `StorageClasses`, die CSI-Speichertreiber für Azure-Datenträger verwenden. Die zusätzlichen CSI-Speicherklassen werden zusammen mit dem Cluster und zusätzlich zu den Standardspeicherklassen in der Struktur erstellt.

- `managed-csi`: Verwendet lokal redundanten Azure-SSD Standard-Speicher (LRS) zum Erstellen eines verwalteten Datenträgers.
- `managed-csi-premium`: Verwendet lokal redundanten Azure-Premium-Speicher (LRS) zum Erstellen verwalteter Datenträger.

Die Freigaberichtlinie in beiden Speicherklassen stellt sicher, dass beim Löschen eines persistenten Volumes auch der zugrunde liegende Azure-Datenträger gelöscht wird. Die Speicherklassen konfigurieren die persistenten Volumes auch so, dass sie erweiterbar sind. Sie müssen nur den persistenten Volumeanspruch (PVC) mit der neuen Größe bearbeiten.

Um diese Speicherklassen zu verwenden, erstellen Sie einen [PVC](concepts-storage.md#persistent-volume-claims) und den entsprechenden Pod, der auf diesen verweist und ihn verwendet. Ein Anspruch auf ein persistentes Volume wird verwendet, um basierend auf einer Speicherklasse automatisch Speicher bereitzustellen. Ein Anspruch auf ein persistentes Volume kann eine der vorab erstellten Speicherklassen oder eine benutzerdefinierte Speicherklasse verwenden, um einen verwalteten Azure-Datenträger für die gewünschte SKU und Größe zu erstellen. Wenn Sie eine Poddefinition erstellen, wird der Anspruch auf ein persistentes Volume angegeben, um den gewünschten Speicher anzufordern.

Erstellen Sie einen Beispielpod und einen entsprechenden PVC mit dem Befehl [kubectl apply][kubectl-apply]:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Nachdem sich der Pod im Zustand „Wird ausgeführt“ befindet, erstellen Sie eine neue Datei namens `test.txt`.

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Sie können jetzt überprüfen, ob der Datenträger ordnungsgemäß eingebunden ist, indem Sie den folgenden Befehl ausführen und überprüfen, ob Sie Datei `test.txt` in der Ausgabe angezeigt wird:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Erstellen einer benutzerdefinierten Speicherklasse

Die Standardspeicherklassen eignen sich für die gängigsten Szenarien, aber nicht für alle. In einigen Fällen möchten Sie möglicherweise Ihre eigene Speicherklasse mit eigenen Parametern anpassen. Wir verfügen z. B. über ein Szenario, in dem Sie vielleicht die `volumeBindingMode`-Klasse ändern möchten.

Die Standardspeicherklassen verwenden eine `volumeBindingMode: Immediate`-Klasse, die sicherstellt, dass dies sofort nach der Erstellung des PVCs erfolgt. In Fällen, in denen Ihre Knotenpools durch die Topologie eingeschränkt sind, z. B. unter Verwendung von Verfügbarkeitszonen, würden PVs ohne Kenntnis der Planungsanforderungen des Pods gebunden oder bereitgestellt werden (in diesem Fall in einer bestimmten Zone).

Um dieses Szenario zu behandeln, können Sie `volumeBindingMode: WaitForFirstConsumer` verwenden, wodurch das Binden und Bereitstellen eines PVs verzögert wird, bis ein Pod erstellt wird, der den PVC verwendet. Auf diese Weise wird das PV konform und in der Verfügbarkeitszone (oder einer anderen Topologie) bereitgestellt, die durch die Planungseinschränkungen des Pods angegeben ist.

Erstellen Sie eine Datei namens `sc-azuredisk-csi-waitforfirstconsumer.yaml`, und fügen Sie das folgende Manifest ein.
Die Speicherklasse ist dieselbe wie unsere `managed-csi`-Speicherklasse, aber mit einer anderen `volumeBindingMode`-Klasse.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Erstellen Sie die Speicherklasse mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie die Datei `sc-azuredisk-csi-waitforfirstconsumer.yaml` an:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Volumesnapshots

Der CSI-Treiber des Azure-Datenträgers unterstützt das Erstellen von [Momentaufnahmen persistenter Volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Im Rahmen dieser Funktion kann der Treiber entweder *vollständige* oder [*inkrementelle* Momentaufnahmen](../virtual-machines/windows/disks-incremental-snapshots.md) ausführen, je nach dem Wert, der im Parameter `incremental` festgelegt wurde (standardmäßig ist dies „true“).

Ausführliche Informationen zu allen Parametern finden Sie unter [Klassenparameter der Volumemomentaufnahmen](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Erstellen einer Volumemomentaufnahme

Als Beispiel für diese Funktion erstellen Sie eine [Volumemomentaufnahmenklasse](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) mit dem Befehl [kubectl apply][kubectl-apply]:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Erstellen Sie jetzt eine [Volumemomentaufnahme](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) mit dem Anspruch auf ein persistentes Volume (PVC), den Sie [zu Beginn dieses Tutorials dynamisch erstellt haben](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) (`pvc-azuredisk`).


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Überprüfen Sie, ob die Momentaufnahme ordnungsgemäß erstellt wurde:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Erstellen eines neuen PVCs auf Basis einer Volumemomentaufnahme

Sie können einen neuen PVC auf der Grundlage einer Volumemomentaufnahme erstellen. Verwenden Sie die im vorigen Schritt erstellte Momentaufnahme, und erstellen Sie einen [neuen PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) sowie einen [neuen Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml), um ihn zu verwenden.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Zum Schluss stellen wir sicher, dass es sich um denselben PVC handelt, der zuvor erstellt wurde, indem wir den Inhalt überprüfen.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Wie erwartet, können wir immer noch unsere zuvor erstellte Datei `test.txt` sehen.

## <a name="clone-volumes"></a>Klonen von Volumes

Ein geklontes Volumen ist als Duplikat eines vorhandenen Kubernetes-Volumens definiert. Weitere Informationen zum Klonen von Volumes in Kubernetes finden Sie in der konzeptionellen Dokumentation zum [Klonen von Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Der CSI-Treiber für Azure-Datenträger unterstützt das Klonen von Volumes. Erstellen Sie zur Veranschaulichung ein [geklontes Volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) des zuvor erstellten [Volumes](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` und [einen neuen Pod, um es zu verwenden](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Wir können jetzt den Inhalt des geklonten Volumes überprüfen, indem wir den folgenden Befehl ausführen und bestätigen, dass wir unsere erstellte Datei `test.txt` immer noch sehen.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Ändern der Größe eines persistenten Volumes

Sie können stattdessen ein größeres Volume für einen Anspruch auf ein persistentes Volume (PVC) anfordern. Bearbeiten Sie das PVC-Objekt, und geben Sie eine höhere Größe an. Diese Änderung löst die Erweiterung des zugrunde liegenden Volumes für den Anspruch auf ein persistentes Volume aus.

> [!NOTE]
> Für den Anspruch wird nie ein neues persistentes Volume erstellt. Stattdessen wird die Größe eines vorhandenen Volumes geändert.

In AKS gestattet die integrierte Speicherklasse `managed-csi` bereits Erweiterungen. Verwenden Sie daher [den zuvor erstellten Anspruch auf ein persistentes Volume mit dieser Speicherklasse](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Der PVC hat ein persistentes 10-Gi-Volume angefordert. Sie können dies überprüfen, indem Sie Folgendes ausführen:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Derzeit unterstützt der CSI-Treiber des Azure-Datenträgers nur die Änderung der Größe von PVCs, denen keine Pods zugeordnet sind (und dessen Volume nicht auf einem bestimmten Knoten eingebunden ist).

Lassen Sie uns daher den zuvor erstellten Pod löschen:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Erweitern Sie den PVC, indem Sie das Feld `spec.resources.requests.storage` vergrößern:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Vergewissern Sie sich, dass das Volume jetzt größer ist:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Der PVC wird erst dann die neue Größe widerspiegeln, wenn ihm wieder ein Pod zugeordnet ist.

Lassen Sie uns einen neuen Pod erstellen:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Und schließlich bestätigen Sie die Größe des PVCs und im Inneren des Pods erfolgt Folgendes:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Freigegebener Datenträger

[Freigegebene Azure-Datenträger](../virtual-machines/disks-shared.md) sind ein Feature verwalteter Azure-Datenträger, mit dem ein Azure-Datenträger gleichzeitig an mehrere Agent-Knoten angefügt werden kann. Durch das Anfügen eines verwalteten Datenträgers an mehrere Agent-Knoten können Sie z. B. neue gruppierte Anwendungen in Azure bereitstellen oder bereits vorhandene gruppierte Anwendungen zu Azure migrieren.

> [!IMPORTANT] 
> Zurzeit wird nur das Blockgerät (`volumeMode: Block`) vom CSI-Treiber für Azure-Datenträger unterstützt. Anwendungen sollten die Koordination und Steuerung von Schreibvorgängen, Lesevorgängen, Sperren, Caches, Bereitstellungen und Fencing auf dem freigegebenen Datenträger verwalten, der als einfaches Blockgerät verfügbar gemacht wird.

Erstellen Sie eine Datei mit dem Namen `shared-disk.yaml`, indem Sie den folgenden Befehl kopieren, der die Speicherklasse des freigegebenen Datenträgers und das PVC enthält:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Erstellen Sie die Speicherklasse mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie die Datei `shared-disk.yaml` an:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Erstellen Sie eine Datei namens `deployment-shared.yml`, indem Sie den folgenden Befehl kopieren:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Erstellen Sie die Bereitstellung mit dem Befehl [kubectl apply][kubectl-apply], und geben Sie die Datei `deployment-shared.yml` an:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Überprüfen Sie abschließend das Blockgerät im Pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-Container

Der CSI-Treiber des Azure-Datenträgers unterstützt auch Windows-Knoten und -Container. Wenn Sie Windows-Container verwenden möchten, befolgen Sie das [Tutorial zu Windows-Containern](windows-container-cli.md), um einen Windows-Knotenpool hinzuzufügen.

Wenn Sie über einen Windows-Knotenpool verfügen, können Sie jetzt die integrierten Speicherklassen wie `managed-csi` verwenden. Sie können einen [Windows-basierten zustandsbehafteten Beispielsatz](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) bereitstellen, mit dem Zeitstempel in der Datei `data.txt` gespeichert werden, indem Sie den folgenden Befehl mit dem Befehl [kubectl apply][kubectl-apply] bereitstellen:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Sie können jetzt den Inhalt des Volumes überprüfen, indem Sie Folgendes ausführen:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von CSI-Treibern für Azure Files finden Sie unter [Verwenden von Azure Files mit CSI-Treibern](azure-files-csi.md).
- Weitere Informationen zu bewährten Methoden bei der Speicherung finden Sie unter [Best Practices für Speicherung und Sicherungen in Azure Kubernetes Service][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register