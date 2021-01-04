---
title: Konzepte – Speicher in Azure Kubernetes Service (AKS)
description: Informationen zu Speicher in Azure Kubernetes Service (AKS) einschließlich Volumes, persistente Volumes, Speicherklassen und Ansprüche
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed38625703397c9ba5021e84cd3118f30fa83c7
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900942"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Speicheroptionen für Anwendungen in Azure Kubernetes Service (AKS)

Anwendungen, die in Azure Kubernetes Service (AKS) ausgeführt werden, müssen vielleicht Daten speichern und abrufen. Bei einigen Anwendungsworkloads kann dieser Datenspeicher lokale, schnelle Speicherung auf dem Knoten verwenden, der nicht mehr benötigt wird, wenn die Pods gelöscht sind. Andere Anwendungsworkloads benötigen möglicherweise Speicher, der auf reguläreren Datenvolumes innerhalb der Azure-Plattform erhalten bleibt. Mehrere Pods müssen möglicherweise die gleichen Datenvolumes gemeinsam nutzen oder Datenvolumes erneut anfügen, wenn der Pod auf einem anderen Knoten neu eingeplant wird. Abschließend müssen Sie möglicherweise vertrauliche Daten oder Informationen zur Anwendungskonfiguration in Pods einfügen.

![Speicheroptionen für Anwendungen in einem Azure Kubernetes Service-Cluster (AKS)](media/concepts-storage/aks-storage-options.png)

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie für Ihre Anwendungen in AKS Speicher bereitstellen:

- [Volumes](#volumes)
- [Persistente Volumes](#persistent-volumes)
- [Speicherklassen](#storage-classes)
- [Ansprüche auf persistente Volumes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Anwendungen müssen häufig Daten speichern und abrufen können. Da Kubernetes einzelne Pods in der Regel als verwerfbare, kurzlebige Ressourcen behandelt, stehen verschiedene Ansätze zur Verfügung, wie Anwendungen Daten nach Bedarf verwenden und beibehalten können. Ein *Volume* ist eine Möglichkeit, Daten während des Lebenszyklus der Anwendung Pods übergreifend zu speichern, abzurufen und beizubehalten.

Herkömmliche Volumes zum Speichern und Abrufen von Daten werden als Kubernetes-Ressourcen erstellt, die durch Azure Storage abgesichert sind. Sie können diese Datenvolumes manuell erstellen, damit sie direkt Pods zugewiesen werden, oder durch Kubernetes automatisch erstellen lassen. Diese Datenvolumes können Azure Disk oder Azure Files verwenden:

- *Azure Disk* kann zum Erstellen einer Kubernetes- *DataDisk* -Ressource verwendet werden. Azure Disk kann von Hochleistungs-SSDs gesicherten Azure Storage Premium oder von regulären Festplatten gesicherten Azure-Standardspeicher verwenden. Verwenden Sie für die meisten Produktionsumgebungen und Entwicklungsworkloads Storage Premium. Azure Disk-Datenträger werden als *ReadWriteOnce* bereitgestellt, daher sind sie nur für einen einzelnen Pod verfügbar. Verwenden Sie für die Speichervolumes, auf die mehrere Pods gleichzeitig zugreifen können, Azure Files.
- Mit *Azure Files* kann eine SMB 3.0-Dateifreigabe bereitgestellt werden, die durch ein Azure Storage-Konto auf Pods gesichert ist. Mit Azure Files können Sie Daten mehrere Knoten und Pods übergreifend freigeben. Files kann durch reguläre HDDs unterstützte Azure Storage Standard-Instanzen oder durch Hochleistungs-SSDs unterstützte Azure Storage Premium-Instanzen verwenden.

In Kubernetes können Volumes mehr als nur einen herkömmlichen Datenträger darstellen, auf dem Informationen gespeichert und abgerufen werden können. Kubernetes-Volumes können auch als Möglichkeit zum Einfügen von Daten in einen Pod zur Verwendung von den Containern genutzt werden. Zu den üblichen zusätzlichen Volumetypen in Kubernetes gehören:

- *emptyDir* : Dieses Volume wird häufig als temporärer Speicher für einen Pod verwendet. Alle Container in einem Pod können auf die Daten des Volumes zugreifen. Auf diesen Volumetyp geschriebene Daten werden nur für die Lebensdauer des Pods beibehalten – wenn der Pod gelöscht wird, wird das Volume gelöscht. Dieses Volume verwendet in der Regel den zugrunde liegenden lokalen Knotendatenträger-Speicher, obwohl es auch nur im Arbeitsspeicher des Knotens vorhanden sein kann.
- *secret* : Dieses Volume wird verwendet, um sensible Daten, wie z.B. Kennwörter, in Pods einzufügen. Zuerst erstellen Sie ein Geheimnis mit der Kubernetes-API. Wenn Sie Ihren Pod oder die Bereitstellung definieren, kann ein bestimmtes Geheimnis angefordert werden. Geheimnisse werden nur für Knoten bereitgestellt, die über einen eingeplanten Pod verfügen, der es benötigt, und das Geheimnis wird in *tmpfs* gespeichert, nicht auf den Datenträger geschrieben. Wenn der letzte Pod auf einem Knoten gelöscht wird, der ein Geheimnis benötigt, wird das Geheimnis aus dem Verzeichnis „tmpfs“ des Knotens gelöscht. Geheimnisse werden in einem bestimmten Namespace gespeichert, und nur Pods im gleichen Namespace können darauf zugreifen.
- *ConfigMap* : Dieser Volumetyp wird verwendet, um Schlüssel-Wert-Paar-Eigenschaften in Pods einzufügen, z.B. Informationen zur Anwendungskonfiguration. Anstatt Informationen zur Anwendungskonfiguration in einem Containerimage zu definieren, können Sie sie als Kubernetes-Ressource definieren, die problemlos aktualisiert und bei deren Bereitstellung auf neue Instanzen von Pods angewendet werden kann. Wie bei der Verwendung eines Geheimnisses erstellen Sie zunächst eine ConfigMap mit der Kubernetes-API. Diese ConfigMap kann dann angefordert werden, wenn Sie einen Pod oder eine Bereitstellung definieren. ConfigMaps werden in einem bestimmten Namespace gespeichert, und nur Pods im gleichen Namespace können darauf zugreifen.

## <a name="persistent-volumes"></a>Persistente Volumes

Volumes werden als Teil des Podlebenszyklus definiert und erstellt und sind nur solange vorhanden, bis der Pod gelöscht wird. Wenn Pods während eines Wartungsereignisses auf einem anderen Host neu eingeplant werden, erwarten sie häufig, dass ihr Speicher erhalten bleibt, insbesondere in StatefulSets. Ein *persistentes Volume* (PV) ist eine von der Kubernetes-API erstellte und verwaltete Speicherressource, die unabhängig von der Lebensdauer eines einzelnen Pods vorhanden sein kann.

Azure Disk oder Azure Files dient zum Bereitstellen des PersistentVolume. Wie im vorherigen Abschnitt zu Volumes erwähnt, wird die Auswahl von Azure Disk oder Azure Files häufig von der Notwendigkeit des gleichzeitigen Zugriffs auf die Daten oder die Leistungsstufe bestimmt.

![Persistente Volumes in einem Azure Kubernetes Service-Cluster (AKS)](media/concepts-storage/persistent-volumes.png)

Ein PersistentVolume kann *statisch* von einem Clusteradministrator oder *dynamisch* vom Kubernetes-API-Server erstellt werden. Wenn ein Pod eingeplant ist und Speicher anfordert, der zurzeit nicht verfügbar ist, kann Kubernetes den zugrunde liegenden Speicher von Azure Disk oder Azure Files erstellen und dem Pod anfügen. Dynamische Bereitstellung verwendet eine *StorageClass* zum Identifizieren des Azure-Speichertyps, der erstellt werden muss.

## <a name="storage-classes"></a>Speicherklassen

Sie können zum Definieren verschiedener Speicherstufen, z.B. „Premium“ und „Standard“, eine *StorageClass* erstellen. Die StorageClass definiert auch die *reclaimPolicy* . Diese reclaimPolicy steuert das Verhalten der zugrunde liegenden Azure-Speicherressource, wenn die Pods gelöscht werden und das persistente Volume vielleicht nicht mehr benötigt wird. Die zugrunde liegende Speicherressource kann gelöscht oder für die Verwendung mit einem zukünftigen Pod beibehalten werden.

In AKS werden vier anfängliche `StorageClasses` für den Cluster erstellt, der die Plug-Ins zur strukturinternen Speicherung verwendet:

- `default`: Verwendet Azure-StandardSSD-Speicher zum Erstellen eines verwalteten Datenträgers. Mit der Freigaberichtlinie wird sichergestellt, dass der zugrunde liegende Azure Disk-Datenträger gelöscht wird, wenn das persistente Volume gelöscht wird, das ihn verwendet hat.
- `managed-premium`: Verwendet Azure Storage Premium zum Erstellen verwalteter Datenträger. Mit der Freigaberichtlinie wird wieder sichergestellt, dass der zugrunde liegende Azure Disk-Datenträger gelöscht wird, wenn das persistente Volume gelöscht wird, das ihn verwendet hat.
- `azurefile`: Verwendet Azure Storage Standard zum Erstellen einer Azure-Dateifreigabe. Mit der Freigaberichtlinie wird sichergestellt, dass die zugrunde liegende Azure-Dateifreigabe gelöscht wird, wenn das persistente Volume gelöscht wird, das sie verwendet hat.
- `azurefile-premium`: Verwendet Azure Storage Premium zum Erstellen einer Azure-Dateifreigabe. Mit der Freigaberichtlinie wird sichergestellt, dass die zugrunde liegende Azure-Dateifreigabe gelöscht wird, wenn das persistente Volume gelöscht wird, das sie verwendet hat.

Cluster, die die neuen externen CSI-Plug-Ins (Container Storage Interface) (Vorschau) verwenden, werden die folgenden zusätzlichen `StorageClasses` erstellt:
- `managed-csi`: Verwendet lokal redundanten Azure-StandardSSD-Speicher (LRS) zum Erstellen eines verwalteten Datenträgers. Mit der Freigaberichtlinie wird sichergestellt, dass der zugrunde liegende Azure Disk-Datenträger gelöscht wird, wenn das persistente Volume gelöscht wird, das ihn verwendet hat. Mit der Speicherklasse werden auch die persistenten Volumes so konfiguriert, dass sie erweiterbar sind. Sie müssen lediglich den Anspruch der persistenten Volumes entsprechend der neuen Größe anpassen.
- `managed-csi-premium`: Verwendet lokal redundanten Azure-Premium-Speicher (LRS) zum Erstellen eines verwalteten Datenträgers. Mit der Freigaberichtlinie wird wieder sichergestellt, dass der zugrunde liegende Azure Disk-Datenträger gelöscht wird, wenn das persistente Volume gelöscht wird, das ihn verwendet hat. Zudem ermöglicht diese Speicherklasse auch eine Erweiterung von persistenten Volumes.
- `azurefile-csi`: Verwendet Azure Storage Standard zum Erstellen einer Azure-Dateifreigabe. Mit der Freigaberichtlinie wird sichergestellt, dass die zugrunde liegende Azure-Dateifreigabe gelöscht wird, wenn das persistente Volume gelöscht wird, das sie verwendet hat.
- `azurefile-csi-premium`: Verwendet Azure Storage Premium zum Erstellen einer Azure-Dateifreigabe. Mit der Freigaberichtlinie wird sichergestellt, dass die zugrunde liegende Azure-Dateifreigabe gelöscht wird, wenn das persistente Volume gelöscht wird, das sie verwendet hat.

Wenn keine StorageClass für ein persistentes Volume angegeben wird, wird die standardmäßige StorageClass verwendet. Achten Sie beim Anfordern von persistenten Volumes darauf, dass sie den Speicher verwenden, den Sie benötigen. Sie können eine StorageClass für zusätzliche Anforderungen mit `kubectl` erstellen. Im folgenden Beispiel werden Managed Disks Premium verwendet und festgelegt, dass der zugrunde liegende Azure Disk-Datenträger *beibehalten* werden soll, wenn der Pod gelöscht wird:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS stimmt die Standardspeicherklassen ab und überschreibt alle Änderungen, die Sie an diesen Speicherklassen vornehmen.

## <a name="persistent-volume-claims"></a>Ansprüche auf persistente Volumes

Ein PersistentVolumeClaim fordert entweder Disk- oder Files-Speicher von bestimmter StorageClass, bestimmtem Zugriffsmodus und bestimmter Größe an. Der Kubernetes-API-Server kann die zugrunde liegende Speicherressource in Azure dynamisch bereitstellen, wenn basierend auf der definierten StorageClass keine Ressourcen zum Erfüllen des Anspruchs vorhanden sind. Sobald die Verbindung des Volumes mit dem Pod hergestellt ist, enthält die Poddefinition die Volumebereitstellung.

![Ansprüche auf persistente Volumes in einem Azure Kubernetes Service-Cluster (AKS)](media/concepts-storage/persistent-volume-claims.png)

Sobald dem anfordernden Pod eine verfügbare Speicherressource zugewiesen ist, ist ein PersistentVolume an einen PersistentVolumeClaim *gebunden* . Es gibt eine 1:1-Zuordnung persistenter Volumes zu Ansprüchen.

Das folgende Beispiel eines YAML-Manifests zeigt einen Anspruch eines persistenten Volumes, der die StorageClass *managed-premium* verwendet und einen Datenträger der Größe *5Gi* anfordert:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Wenn Sie eine Poddefinition erstellen, wird der Anspruch auf ein persistentes Volume angegeben, um den gewünschten Speicher anzufordern. Sie legen dann auch *volumeMount* zum Lesen und Schreiben von Daten für Ihre Anwendungen fest. Das folgende Beispiel-YAML-Manifest zeigt, wie der vorherige Anspruch auf ein persistentes Volume verwendet werden kann, um ein Volume unter */mnt/azure* einzubinden:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Nächste Schritte

Entsprechenden bewährte Methoden finden Sie unter [Bewährte Methoden für die Speicherung und Sicherungen in AKS][operator-best-practices-storage].

Informationen zum Erstellen von dynamischen und statischen Volumes, die Azure Disk oder Azure Files verwenden, finden Sie in den folgenden „Gewusst-wie“-Artikeln:

- [Erstellen eines statischen Volumes mit Azure Disks][aks-static-disks]
- [Erstellen eines statischen Volumes mit Azure Files][aks-static-files]
- [Erstellen eines dynamischen Volumes mit Azure Disks][aks-dynamic-disks]
- [Erstellen eines dynamischen Volumes mit Azure Files][aks-dynamic-files]

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Identität][aks-concepts-identity]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
