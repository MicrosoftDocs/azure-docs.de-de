---
title: Erstellen eines NFS-Ubuntu-Servers (Network File System) für die Verwendung durch Pods von Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie manuell ein Volume eines NFS-Ubuntu Linux-Servers für die Verwendung mit Pods in Azure Kubernetes Service (AKS) erstellen.
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468445"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Manuelles Erstellen und Verwenden eines Volumes eines Linux-NFS-Servers (Network File System) mit Azure Kubernetes Service (AKS)
Die Datenfreigabe zwischen Containern ist häufig eine notwendige Komponente containerbasierter Dienste und Anwendungen. Sie haben in der Regel verschiedene Pods, die Zugriff auf die gleichen Informationen auf einem externen permanenten Datenträger benötigen.    
Während Azure Files infrage kommt, kann permanenter freigegebener Speicher auch durch Erstellen eines NFS-Servers auf einer Azure-VM bereitgestellt werden. 

Dieser Artikel zeigt Ihnen, wie Sie einen NFS-Server auf einem virtuellen Ubuntu-Computer erstellen. Sie erfahren außerdem, wie Sie Ihren AKS-Containern Zugriff auf dieses gemeinsam genutzte Dateisystem erteilen.

## <a name="before-you-begin"></a>Voraussetzungen
Es wird vorausgesetzt, dass Sie über einen AKS-Cluster verfügen. Wenn Sie noch einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Ihr AKS-Cluster muss sich in dem gleichen Netzwerk wie der NFS-Server oder in einem mittels Peering verknüpften befinden. Der Cluster muss in einem vorhandenen VNET erstellt werden; dies kann dasselbe sein, zu dem auch Ihre VM gehört.

Die Schritte zum Konfigurieren mit einem vorhandenen VNET werden in der Dokumentation beschrieben: [Erstellen eines AKS-Clusters im virtuellen Netzwerk][aks-virtual-network] und [Tutorial: Herstellen von Verbindungen zwischen virtuellen Netzwerken durch Peerings für virtuelle Netzwerke mit dem Azure-Portal][peer-virtual-networks].

Außerdem wird davon ausgegangen, dass Sie eine Ubuntu-Linux-VM (z.B. 18.04 LTS) erstellt haben. Einstellungen und Größe Ihrer Wahl können über Azure bereitgestellt werden. Informationen zum Linux-Schnellstart finden Sie unter [Tutorial: Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle][linux-create].

Wenn Sie zuerst Ihren AKS-Cluster bereitstellen, füllt Azure bei der Bereitstellung Ihres Ubuntu-Computers automatisch das Feld „Virtuelles Netzwerk“ aus, sodass beide sich in demselben VNET befinden. Aber wenn Sie stattdessen mit mittels Peering verknüpften Netzwerken arbeiten möchten, nutzen Sie die obige Dokumentation.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Bereitstellen des NFS-Servers auf einem virtuellen Computer
Mit diesem Skript richten Sie einen NFS-Server auf Ihrem virtuellen Ubuntu-Computer ein:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Der Server wird (aufgrund des Skripts) neu gestartet, und Sie können den NFS-Server in AKS einbinden.

>[!IMPORTANT]  
>Achten Sie darauf, **AKS_SUBNET** durch das richtige aus Ihrem Cluster zu ersetzen, oder öffnen Sie sonst mit „*“ Ihren NFS-Server für alle Ports und Verbindungen.

Nachdem Sie Ihren virtuellen Computer erstellt haben, kopieren Sie das obige Skript in eine Datei. Anschließend können Sie es von Ihrem lokalen Computer oder sonstigen Speicherort folgendermaßen auf die VM verschieben: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Sobald Ihr Skript sich auf dem virtuellen Computer befindet, können Sie eine SSH-Verbindung mit der VM herstellen und es mit dem folgenden Befehl ausführen:
```console
sudo ./nfs-server-setup.sh
```
Wenn bei der Ausführung ein Fehler aufgrund einer Berechtigungsverweigerung auftritt, legen Sie die Ausführungsberechtigung mit dem folgenden Befehl fest:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Herstellen einer Verbindung des AKS-Clusters mit dem NFS-Server
Wir können durch Bereitstellung eines permanenten Volumes und des Anspruchs auf ein permanentes Volume, der angibt, wie auf das Volume zugegriffen wird, eine Verbindung des NFS-Servers mit unserem Cluster herstellen.  
Das Verbinden der beiden Dienste im gleichen Netzwerk oder in mittels Peering verknüpften virtuellen Netzwerken ist erforderlich. Anweisungen zum Einrichten des Clusters im gleichen VNET finden Sie hier: [Erstellen eines AKS-Clusters im virtuellen Netzwerk][aks-virtual-network].

Sobald sie sich im gleichen virtuellen Netzwerk (oder in mittels Peering miteinander verknüpften virtuellen Netzwerken) befinden, müssen Sie ein permanentes Volume und einen Anspruch auf ein permanentes Volume in Ihrem AKS-Cluster bereitstellen. Die Container können dann das NFS-Laufwerk in ihr lokales Verzeichnis einbinden.

Hier sehen Sie das Beispiel einer Kubernetes-Definition für das permanente Volume (diese Definition setzt voraus, dass Ihr Cluster und der virtuelle Computer sich im gleichen VNET befinden):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Ersetzen Sie **NFS_INTERNAL_IP**, **NFS_NAME** und **NFS_EXPORT_FILE_PATH** mit NFS-Serverinformationen.

Außerdem benötigen Sie eine Datei für den Anspruch auf ein permanentes Volume. Hier sehen Sie ein Beispiel dafür, was Sie einbeziehen müssen:

>[!IMPORTANT]  
>**storageClassName** muss eine leere Zeichenfolge bleiben, ansonsten funktioniert der Anspruch nicht.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Problembehandlung
Wenn Sie von einem Cluster aus keine Verbindung mit dem Server herstellen können, verfügt möglicherweise das exportierte Verzeichnis oder das ihm übergeordnete Element nicht über ausreichende Berechtigungen zum Zugriff auf den Server.

Stellen Sie sicher, dass sowohl Ihr Exportverzeichnis als auch das übergeordnete Verzeichnis über 777-Berechtigungen verfüg.

Sie können die Berechtigungen überprüfen, indem Sie den folgenden Befehl ausführen, und die Verzeichnisse sollten *drwxrwxrwx*-Berechtigungen haben:
```console
ls -l
```

## <a name="more-information"></a>Weitere Informationen
Eine vollständige exemplarische Vorgehensweise und Informationen dazu, wie Sie Ihr NFS-Serversetup debuggen, finden Sie in diesem ausführlichen Tutorial:
  - [NFS-Tutorial][nfs-tutorial]

## <a name="next-steps"></a>Nächste Schritte

Entsprechenden bewährte Methoden finden Sie unter [Bewährte Methoden für die Speicherung und Sicherungen in AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
