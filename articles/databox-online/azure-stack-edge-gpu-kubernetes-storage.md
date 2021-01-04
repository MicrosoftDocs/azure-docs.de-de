---
title: Grundlegendes zur Kubernetes-Speicherverwaltung auf einem Azure Stack Edge Pro-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie die Kubernetes-Speicherverwaltung auf einem Azure Stack Edge Pro-Gerät erfolgt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: alkohli
ms.openlocfilehash: 34165071238ca3edf78ab9cca43639c23ce5ed2a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448695"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes-Speicherverwaltung auf dem Azure Stack Edge Pro-GPU-Gerät

Wenn Sie die Computerolle konfigurieren, wird auf Ihrem Azure Stack Edge Pro-Gerät ein Kubernetes-Cluster erstellt. Sobald der Kubernetes-Cluster erstellt wurde, können innerhalb des Kubernetes-Clusters Containeranwendungen in Pods bereitgestellt werden. Es gibt verschiedene Möglichkeiten, um Speicher in Pods in Ihrem Kubernetes-Cluster bereitzustellen. 

In diesem Artikel werden die Methoden zum Bereitstellen von Speicher in einem Kubernetes-Cluster sowohl allgemein als auch im Kontext Ihres Azure Stack Edge Pro-Geräts beschrieben. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Speicheranforderungen für Kubernetes-Pods

Kubernetes-Pods sind zustandslos, aber die Anwendungen, die sie ausführen, sind gewöhnlich zustandsbehaftet. Da Pods kurzlebig sein können und zwischen Kubernetes-Knoten neu gestartet werden, Failover ausführen und verschoben werden, müssen die folgenden Anforderungen für den Speicher erfüllt sein, der dem Pod zugeordnet ist. 

Der Speicher muss folgende Anforderungen erfüllen:

- Er muss sich außerhalb des Pods befinden.
- Er muss vom Pod-Lebenszyklus unabhängig sein.
- Auf ihn muss von allen Kubernetes-Knoten aus zugegriffen werden können.

Damit Sie nachvollziehen können, wie der Speicher für Kubernetes verwaltet wird, müssten Sie zwei API-Ressourcen verstehen: 

- **PersistentVolume (PV)** : Dies ist ein Teil des Speichers im Kubernetes-Cluster. Der Kubernetes-Speicher kann statisch als `PersistentVolume` bereitgestellt werden. Er kann aber auch dynamisch als `StorageClass` bereitgestellt werden.

- **PersistentVolumeClaim (PVC)** : Dies ist eine Speicheranforderung durch einen Benutzer. PVCs nutzen PV-Ressourcen. PVCs können bestimmte Größen und Zugriffsmodi anfordern. 

    Da Benutzer `PersistentVolumes` mit unterschiedlichen Eigenschaften für unterschiedliche Probleme benötigen, müssen Clusteradministratoren eine Vielzahl von `PersistentVolumes` anbieten können, die sich nicht nur durch die Größe und den Zugriffsmodus unterscheiden. Für diese Anforderungen benötigen Sie die Ressource `StorageClass`.

Die Speicherbereitstellung kann statisch oder dynamisch sein. Jeder der Bereitstellungstypen wird in den folgenden Abschnitten erläutert.

## <a name="static-provisioning"></a>Statische Bereitstellung

Kubernetes-Clusteradministratoren können den Speicher statisch bereitstellen. Zu diesem Zweck können sie ein Speicher-Back-End, das auf SMB/NFS-Dateisystemen basiert, iSCSI-Datenträger, die lokal über das Netzwerk in einer lokalen Umgebung angefügt werden, oder Azure Files oder Azure-Datenträger in der Cloud verwenden. Diese Art von Speicher wird nicht standardmäßig bereitgestellt, und Clusteradministratoren müssen diese Bereitstellung planen und verwalten. 
 
Im folgenden Diagramm wird dargestellt, wie statisch bereitgestellter Speicher in Kubernetes genutzt wird: 

![Statische Bereitstellung über PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Dabei werden die folgenden Schritte ausgeführt: 

1. **Bereitstellen des Speichers**: Der Clusteradministrator stellt den Speicher bereit. In diesem Beispiel erstellt der Clusteradministrator mindestens eine SMB-Freigabe, die im Kubernetes-Cluster, der diesen Freigaben entspricht, automatisch persistente Volumeobjekte erstellt. 

1. **Beanspruchen von Speicher**: Übermitteln Sie eine PVC-Bereitstellung, die den Speicher anfordert. Dieser Speicheranspruch ist ein PersistentVolumeClaim (PVC). Wenn die Größe und der Zugriffsmodus des PV dem PVC entsprechen, wird das PVC an das PV gebunden. PVC und PV sind einander 1:1 zugeordnet.

1. **Einbinden von PVC in den Container**: Sobald das PVC an das PV gebunden ist, können Sie dieses PVC in einem Pfad in Ihrem Container einbinden. Wenn die Anwendungslogik im Container aus diesem Pfad liest bzw. in diesen Pfad schreibt, werden die Daten in den SMB-Speicher geschrieben.
 

## <a name="dynamic-provisioning"></a>Dynamische Bereitstellung

Im folgenden Diagramm ist dargestellt, wie statisch bereitgestellter Speicher in Kubernetes genutzt wird: 

![Dynamische Bereitstellung über StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Dabei werden die folgenden Schritte ausgeführt: 

1. **Definieren der Speicherklasse**: Der Clusteradministrator definiert eine Speicherklasse in Abhängigkeit von der Betriebsumgebung Ihres Kubernetes-Clusters. Der Clusteradministrator stellt außerdem einen Bereitsteller bereit. Dabei handelt es sich um einen weiteren Pod oder eine Anwendung, die auf dem Kubernetes-Cluster bereitgestellt wurde. Der Bereitsteller verfügt über alle Details, um die Freigaben dynamisch bereitzustellen.  

1. **Beanspruchen von Speicher**: Sie übermitteln eine Anwendung, die den Speicher beansprucht. Nachdem ein PVC mit diesem Speicherklassenverweis erstellt wurde, wird der Bereitsteller aufgerufen. 

1. **Dynamisches Bereitstellen von Speicher**: Der Bereitsteller erstellt dynamisch die Freigabe, die dem lokalen Datenspeicher zugeordnet ist. Nachdem die Freigabe erstellt wurde, wird automatisch ein PV-Objekt erstellt, das dieser Freigabe entspricht.

1. **Einbinden von PVC in den Container**: Sobald das PVC an das PV gebunden ist, können Sie das PVC in den Container auf die gleiche Weise wie bei der statischen Bereitstellung in einem Pfad einbinden und aus der Freigabe lesen oder in diese schreiben.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Speicherbereitstellung auf dem Azure Stack Edge Pro-Gerät

Auf dem Azure Stack Edge Pro-Gerät werden statisch bereitgestellte `PersistentVolumes` mithilfe der Speicherfunktionen des Geräts erstellt. Wenn Sie eine Freigabe bereitstellen und die Option **Freigabe mit Edgecomputing verwenden** aktiviert ist, wird durch diese Aktion automatisch eine PV-Ressource im Kubernetes-Cluster erstellt.

![Erstellen einer lokalen Freigabe im Azure-Portal für die statische Bereitstellung](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Damit Sie Cloudtiering verwenden können, erstellen Sie eine Edge-Cloudfreigabe, bei der die Option „Freigabe mit Edgecomputing verwenden“ aktiviert ist. Für diese Freigabe wird automatisch ein PV erstellt. Alle Anwendungsdaten, die Sie in die Edge-Freigabe schreiben, werden per Tiering in die Cloud übertragen. 

![Erstellen von Cloudfreigaben im Azure-Portal für die statische Bereitstellung](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Sie können sowohl SMB- als auch NFS-Freigaben erstellen, um PVs statisch auf einem Azure Stack Edge Pro-Gerät bereitzustellen. Nachdem die PV-Bereitstellung erfolgt ist, übermitteln Sie ein PVC, um diesen Speicher zu beanspruchen. Im Folgenden finden Sie ein Beispiel für eine `yaml`-Datei für die PVC-Bereitstellung, die den Speicher beansprucht und die von Ihnen bereitgestellten Freigaben verwendet.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Weitere Informationen finden Sie unter [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit PersistentVolume auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Für den Zugriff auf denselben statisch bereitgestellten Speicher lauten die entsprechenden Volumebereitstellungsoptionen für Speicherbindungen für IoT wie folgt. `/home/input` ist der Pfad, über den innerhalb des Containers auf das Volume zugegriffen werden kann.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro verfügt auch über ein integriertes `StorageClass`-Element namens `ase-node-local`, das einen an den Kubernetes-Knoten angefügten Datenträgerspeicher verwendet. Dieses `StorageClass`-Element unterstützt die dynamische Bereitstellung. Sie können in den Pod-Anwendungen einen `StorageClass`-Verweis erstellen, und ein PV wird automatisch erstellt. Weitere Informationen finden Sie im [Kubernetes-Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) zum Abfragen nach `ase-node-local StorageClass`.

![Integrierte Speicherklasse im Kubernetes-Dashboard](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Weitere Informationen finden Sie unter [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit StorageClass auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Auswählen des Speichertyps

In Abhängigkeit von der Workload, die Sie bereitstellen, müssen Sie möglicherweise den Speichertyp auswählen. 

- Wenn Sie den `ReadWriteMany`-Zugriffsmodus für die `PersistentVolumes` benötigen, in denen die Volumes von vielen bereitgestellten Knoten mit Lese-/Schreibzugriff bereitgestellt werden, verwenden Sie die statische Bereitstellung für die SMB/NFS-Freigaben.

- Wenn die Anwendungen, die Sie bereitstellen, POSIX-Complianceanforderung aufweisen (z. B. Anwendungen wie MongoDB, PostgreSQL, MySQL oder Prometheus), verwenden Sie die integrierte StorageClass. Die Zugriffsmodi sind `ReadWriteOnce`, oder das Volume wird von einem einzelnen Knoten mit Lese-/Schreibzugriff bereitgestellt. 


Weitere Informationen zu den Zugriffsmodi finden Sie unter [Zugriffsmodi für Kubernetes-Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie Sie ein `PersistentVolume` statisch bereitstellen können, finden Sie unter:

- [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit PersistentVolume auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Informationen dazu, wie Sie ein `StorageClass` dynamisch bereitstellen können, finden Sie unter:

- [Verwenden von kubectl zum Ausführen einer zustandsbehafteten Kubernetes-Anwendung mit StorageClass auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
