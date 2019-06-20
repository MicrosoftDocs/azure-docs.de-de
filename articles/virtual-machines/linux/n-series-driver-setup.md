---
title: Einrichtung von GPU-Treibern für die Azure N-Serie unter Linux | Microsoft-Dokumentation
description: Einrichtung von NVIDIA-GPU-Treibern für virtuelle Computer der N-Serie mit Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 769d3dda7b1e49612279c9bfa6a3dd586e50e4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479108"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installieren von NVIDIA GPU-Treibern für virtuelle Computer der Serie N mit Linux

Um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie unter Linux nutzen zu können, müssen NVIDIA-GPU-Treiber installiert werden. Mit der [NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-linux.md) werden entsprechende NVIDIA-CUDA- oder GRID-Treiber auf einem virtuellen Computer der N-Serie installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie der Azure-Befehlszeilenschnittstelle oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Distributionen und Bereitstellungsschritten finden Sie in der [Dokumentation zur NVIDIA-GPU-Treibererweiterung](../extensions/hpccompute-gpu-linux.md).

Wenn Sie GPU-Treiber manuell installieren möchten, finden Sie in diesem Artikel Informationen zu unterstützten Distributionen und Treibern sowie Schritte zur Installation und Überprüfung. Informationen zur manuellen Einrichtung von Treibern stehen auch für [Windows-VMs](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zur Verfügung.

Informationen zu Spezifikationen von virtuellen Computern der N-Serie, Speicherkapazitäten und Details zu den Datenträgern finden Sie unter [GPU-optimierte Größen von virtuellen Linux-Computern](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installieren von CUDA-Treibern für virtuelle Computer der N-Serie

Hier werden Schritte zum Installieren von CUDA-Treibern auf virtuellen Computern der N-Serie über das NVIDIA CUDA Toolkit beschrieben. 


C- und C++-Entwickler können optional das vollständige Toolkit zum Erstellen GPU-beschleunigter Anwendungen installieren. Weitere Informationen finden Sie im [CUDA-Installationshandbuch](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Stellen Sie zum Installieren von CUDA-Treibern eine SSH-Verbindung mit den einzelnen virtuellen Computern her. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass das System über eine CUDA-fähige GPU verfügt:

```bash
lspci | grep -i NVIDIA
```
Die Ausgabe sieht in etwa wie das folgende Beispiel aus (zeigt eine NVIDIA Tesla K80-Karte):

![lspci-Befehlsausgabe](./media/n-series-driver-setup/lspci.png)

Führen Sie dann die für Ihre Verteilung spezifischen Installationsbefehle aus.

### <a name="ubuntu"></a>Ubuntu 

1. Laden Sie die CUDA-Treiber von der NVIDIA-Website herunter, und installieren Sie sie. Beispielsweise für Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   Die Installation kann einige Minuten dauern.

2. Um das vollständige CUDA-Toolkit optional zu installieren, geben Sie Folgendes ein:

   ```bash
   sudo apt-get install cuda
   ```

3. Starten Sie die VM neu, und fahren Sie mit der Überprüfung der Installation fort.

#### <a name="cuda-driver-updates"></a>CUDA-Treiberupdates

Sie sollten CUDA-Treiber nach der Bereitstellung in regelmäßigen Abständen aktualisieren.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS oder Red Hat Enterprise Linux

1. Aktualisieren Sie den Kernel (empfohlen). Wenn Sie den Kernel nicht aktualisieren, stellen sicher, dass die Versionen von `kernel-devel` und `dkms` für Ihren Kernel geeignet sind.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Stellen Sie die Verbindung mit dem virtuellen Computer wieder her, und setzen Sie die Installation mit den folgenden Befehlen fort:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Die Installation kann einige Minuten dauern. 

4. Um das vollständige CUDA-Toolkit optional zu installieren, geben Sie Folgendes ein:

   ```bash
   sudo yum install cuda
   ```

5. Starten Sie die VM neu, und fahren Sie mit der Überprüfung der Installation fort.

### <a name="verify-driver-installation"></a>Überprüfen der Treiberinstallation

Stellen Sie zum Abfragen des GPU-Gerätestatus eine SSH-Verbindung mit der VM her, und führen Sie das mit dem Treiber installierte Befehlszeilen-Hilfsprogramm [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) aus. 

Wenn der Treiber installiert wurde, wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass eine **GPU-Auslastung** von 0 % angezeigt wird, sofern gerade keine GPU-Workload auf dem virtuellen Computer ausgeführt wird. Die Treiberversion und die GPU-Informationen weichen möglicherweise von den angezeigten Informationen ab.

![NVIDIA-Gerätestatus](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-Netzwerkkonnektivität

RDMA-Netzwerkkonnektivität kann auf virtuellen Computern der N-Serie (z. B. NC24r) aktiviert werden, die RDMA-fähig sind und in derselben Verfügbarkeitsgruppe bzw. in einer einzelnen Platzierungsgruppe in einer VM-Skalierungsgruppe bereitgestellt werden. Das RDMA-Netzwerk unterstützt Message Passing Interface-Datenverkehr (MPI) für Anwendungen mit Intel MPI 5.x oder einer höheren Version. Nachfolgend werden weitere Anforderungen aufgeführt:

### <a name="distributions"></a>Verteilungen

Stellen Sie RDMA-fähige VMs der N-Serie über eines der Images aus dem Azure Marketplace bereit, das RDMA-Konnektivität für VMs der N-Serie unterstützt:
  
* **Ubuntu 16.04 LTS**: Konfigurieren Sie RDMA-Treiber auf der VM, und registrieren Sie sich bei Intel, um Intel MPI herunterzuladen:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **7.4 HPC (CentOS-basiert)** : Auf der VM sind RDMA-Treiber und Intel MPI 5.1 installiert.

## <a name="install-grid-drivers-on-nv-or-nvv2-series-vms"></a>Installieren von GRID-Treibern für virtuelle Computer der NV- oder NVv2-Serie

Stellen Sie zum Installieren von NVIDIA GRID-Treibern auf virtuellen Computern der NV- oder NVv2-Serie eine SSH-Verbindung mit jedem virtuellen Computer her, und führen Sie die Schritte für Ihre Linux-Distribution aus. 

### <a name="ubuntu"></a>Ubuntu 

1. Führen Sie den Befehl `lspci` aus. Überprüfen Sie, ob die NVIDIA M60-Karte bzw. -Karten als PCI-Geräte angezeigt werden.

2. Installieren Sie die Updates.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   ```
3. Deaktivieren Sie den Nouveau-Kerneltreiber, da er nicht mit dem NVIDIA-Treiber kompatibel ist. (Verwenden Sie den NVIDIA-Treiber nur auf virtuellen NV- oder NVv2-Computern.) Erstellen Sie zu diesem Zweck eine Datei in `/etc/modprobe.d`, und nennen Sie sie `nouveau.conf`. Die Datei muss den folgenden Inhalt enthalten:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Starten Sie den virtuellen Computer neu, und stellen Sie erneut eine Verbindung her. Exit X-Server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Laden Sie den GRID-Treiber herunter, und installieren Sie ihn:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Wenn Sie gefragt werden, ob Sie mit dem Hilfsprogramm „nvidia-xconfig“ Ihre X-Konfigurationsdatei aktualisieren möchten, klicken Sie auf **Ja**.

7. Nachdem die Installation abgeschlossen ist, kopieren Sie „/etc/nvidia/gridd.conf.template“ in eine neue Datei „gridd.conf at location /etc/nvidia/“.

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Fügen Sie Folgendes zu `/etc/nvidia/gridd.conf` hinzu:
 
   ```
   IgnoreSP=FALSE
   ```
9. Starten Sie die VM neu, und fahren Sie mit der Überprüfung der Installation fort.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS oder Red Hat Enterprise Linux 

1. Aktualisieren Sie den Kernel und DKMS (empfohlen). Wenn Sie den Kernel nicht aktualisieren, stellen sicher, dass die Versionen von `kernel-devel` und `dkms` für Ihren Kernel geeignet sind.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   ```

2. Deaktivieren Sie den Nouveau-Kerneltreiber, da er nicht mit dem NVIDIA-Treiber kompatibel ist. (Verwenden Sie den NVIDIA-Treiber nur auf virtuellen NV- oder NV2-Computern.) Erstellen Sie zu diesem Zweck eine Datei in `/etc/modprobe.d`, und nennen Sie sie `nouveau.conf`. Die Datei muss den folgenden Inhalt enthalten:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Starten Sie den virtuellen Computer neu, und installieren Sie die aktuellsten [Linux-Integrationsdienste für Hyper-V und Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Stellen Sie erneut eine Verbindung zum virtuellen Computer her, und führen Sie den Befehl `lspci` aus. Überprüfen Sie, ob die NVIDIA M60-Karte bzw. -Karten als PCI-Geräte angezeigt werden.
 
5. Laden Sie den GRID-Treiber herunter, und installieren Sie ihn:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Wenn Sie gefragt werden, ob Sie mit dem Hilfsprogramm „nvidia-xconfig“ Ihre X-Konfigurationsdatei aktualisieren möchten, klicken Sie auf **Ja**.

7. Nachdem die Installation abgeschlossen ist, kopieren Sie „/etc/nvidia/gridd.conf.template“ in eine neue Datei „gridd.conf at location /etc/nvidia/“.
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Fügen Sie Folgendes zu `/etc/nvidia/gridd.conf` hinzu:
 
   ```
   IgnoreSP=FALSE
   ```
9. Starten Sie die VM neu, und fahren Sie mit der Überprüfung der Installation fort.

### <a name="verify-driver-installation"></a>Überprüfen der Treiberinstallation


Stellen Sie zum Abfragen des GPU-Gerätestatus eine SSH-Verbindung mit der VM her, und führen Sie das mit dem Treiber installierte Befehlszeilen-Hilfsprogramm [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) aus. 

Wenn der Treiber installiert wurde, wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass eine **GPU-Auslastung** von 0 % angezeigt wird, sofern gerade keine GPU-Workload auf dem virtuellen Computer ausgeführt wird. Die Treiberversion und die GPU-Informationen weichen möglicherweise von den angezeigten Informationen ab.

![NVIDIA-Gerätestatus](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-Server
Wenn Sie einen X11-Server für Remoteverbindungen mit einem virtuellen NV- oder NVv2-Computer benötigen, wird [x11vnc](http://www.karlrunge.com/x11vnc/) empfohlen, da damit die Grafikhardwarebeschleunigung ermöglicht wird. Die BusID des M60-Geräts muss der X11-Konfigurationsdatei (normalerweise `etc/X11/xorg.conf`) manuell hinzugefügt werden. Fügen Sie einen `"Device"`-Abschnitt analog zum folgenden Abschnitt hinzu:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Aktualisieren Sie darüber hinaus Ihren `"Screen"`-Abschnitt, um dieses Gerät zu verwenden.
 
Die dezimale Bus-ID kann durch Ausführen von Folgendem ermittelt werden:

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
Die Bus-ID kann sich ändern, wenn ein virtueller Computer neu zugewiesen oder neu gestartet wird. Wenn ein virtueller Computer neu gestartet wird, müssen Sie daher in der X11-Konfiguration zum Aktualisieren der Bus-ID ein Skript erstellen. Erstellen Sie z.B. ein Skript namens `busidupdate.sh` (oder eines anderen Namens Ihrer Wahl) mit ähnlichem Inhalt wie dem folgenden:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Erstellen Sie dann einen Eintrag für Ihr Updateskript in `/etc/rc.d/rc3.d`, damit das Skript beim Systemstart als Stamm aufgerufen wird.

## <a name="troubleshooting"></a>Problembehandlung

* Sie können den Persistenzmodus mit `nvidia-smi` festlegen, damit die Ausgabe des Befehls schneller erfolgt, wenn Sie Karten abfragen müssen. Führen Sie zum Festlegen des Persistenzmodus `nvidia-smi -pm 1` aus. Beachten Sie, dass die Moduseinstellung verloren geht, wenn der virtuelle Computer neu gestartet wird. Sie haben aber die Möglichkeit, per Skript festzulegen, dass der Modus immer beim Start festgelegt werden soll.

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie ein Linux-VM-Image mit den installierten NVIDIA-Treibern erfassen möchten, lesen Sie [Generalisieren und Erfassen eines virtuellen Linux-Computers](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
