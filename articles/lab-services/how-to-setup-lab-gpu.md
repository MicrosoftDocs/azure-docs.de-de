---
title: Einrichten eines Labs mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab mit virtuellen GPU-Computern (Graphics Processing Unit) einrichten.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452376"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Einrichten eines Labs mit virtuellen GPU-Computern

In diesem Artikel wird gezeigt, wie Sie Folgendes durchführen:

- Wählen zwischen GPUs (Graphics Processing Units) für *Visualisierung* und *Compute*.
- Sicherstellen, dass die entsprechenden GPU-Treiber installiert sind.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Wählen zwischen Visualisierungs- und Compute-GPU-Größen
Sie wählen auf der ersten Seite des Assistenten für die Lab-Erstellung in der Dropdownliste **Welche VM-Größe benötigen Sie?** die Größe der VMs aus, die für den Kurs erforderlich sind.  

![Screenshot des Bereichs „Neues Lab“ zum Auswählen einer VM-Größe](./media/how-to-setup-gpu/lab-gpu-selection.png)

Bei diesem Vorgang haben Sie die Möglichkeit, **Visualisierungs**- oder **Compute**-GPUs auszuwählen.  Es ist wichtig, den Typ der GPU auszuwählen, der auf der Software basiert, die Ihre Kursteilnehmer verwenden werden.  

Wie in der folgenden Tabelle beschrieben, ist die *Compute*-GPU-Größe für rechenintensive Anwendungen vorgesehen.  Beispielsweise verwendet der [Kurstyp „Deep Learning in Natural Language Processing“](./class-type-deep-learning-natural-language-processing.md) die **kleine GPU-Größe (Compute)** .  Die Compute-GPU eignet sich für diese Art von Kurs, da die Kursteilnehmer Deep Learning-Frameworks und Tools verwenden, die vom [Data Science Virtual Machine-Image](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) bereitgestellt werden, um Deep Learning-Modelle mit großen Datensätzen zu trainieren.

| Size | Kerne | RAM | BESCHREIBUNG | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (Compute) | -&nbsp;6&nbsp;Kerne<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |Diese Größe eignet sich am besten für computeintensive Anwendungen wie künstliche Intelligenz (KI) und Deep Learning. |

Die *Visualisierungs*-GPU-Größen sind für grafikintensive Anwendungen vorgesehen.  Beispielsweise zeigt der [Kurstyp „SOLIDWORKS Engineering“](./class-type-solidworks.md) die Verwendung der **Kleinen GPU-Größe (Visualisierung)** .  Die Visualisierungs-GPU eignet sich für diese Art von Kurs, da die Kursteilnehmer mit der SOLIDWORKS 3D-CAD-Umgebung (Computer-Aided Design) zum Modellieren und Visualisieren solider Objekte interagieren.

| Size | Kerne | RAM | BESCHREIBUNG | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (Visualisierung) | -&nbsp;6&nbsp;Kerne<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | Diese Größe eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. |
| Mittlere GPU (Visualisierung) | -&nbsp;12&nbsp;Kerne<br>-&nbsp;112&nbsp;GB&nbsp;RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Diese Größe eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. |

> [!NOTE]
> Beim Erstellen eines Classroom-Labs werden möglicherweise einige dieser VM-Größen nicht in der Liste angezeigt. Die Liste wird basierend auf der aktuellen Kapazität des Standorts des Labs aufgefüllt. Wenn der Ersteller des Lab-Kontos [Lab-Erstellern das Auswählen eines Standorts für das Lab gestattet](allow-lab-creator-pick-lab-location.md), können Sie versuchen, einen anderen Ort für das Lab auszuwählen, und dann nachsehen, ob die VM-Größe verfügbar ist. Informationen zur Verfügbarkeit von VMs finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/?products=virtual-machines).

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Sicherstellen, dass die entsprechenden GPU-Treiber installiert sind
Wenn Sie die GPU-Funktionen Ihrer virtuellen Lab-Computer nutzen möchten, stellen Sie sicher, dass die entsprechenden GPU-Treiber installiert sind.  Wenn Sie im Assistenten für die Lab-Erstellung eine GPU-VM-Größe auswählen, können Sie die Option **GPU-Treiber installieren** auswählen.  

![Screenshot von „Neues Lab“mit der Option „GPU-Treiber installieren“](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Wie in der Abbildung oben gezeigt, ist diese Option standardmäßig aktiviert, wodurch sichergestellt wird, dass die *neuesten* Treiber für den Typ von GPU und Image installiert werden, den Sie ausgewählt haben.
- Wenn Sie eine *Compute*-GPU-Größe auswählen, werden die Lab-VMs mit der [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf)-GPU betrieben.  In diesem Fall werden die neuesten [CUDA-Treiber (Compute Unified Device Architecture)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) installiert, die Hochleistungscomputing ermöglichen.
- Wenn Sie eine *Visualisierungs*-GPU-Größe auswählen, werden Ihre Lab-VMs mit der [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf)-GPU und [GRID-Technologie](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf) betrieben.  In diesem Fall werden die neuesten GRID-Treiber installiert, was die Verwendung von grafikintensiven Anwendungen ermöglicht.

### <a name="install-the-drivers-manually"></a>Manuelles Installieren der Treiber
Möglicherweise müssen Sie eine andere Treiberversion als die aktuellste Version installieren.  In diesem Abschnitt wird gezeigt, wie die entsprechenden Treiber manuell installiert werden. Diese hängen davon ab, ob Sie eine *Compute-* GPU oder eine *Visualisierung* GPU verwenden.

#### <a name="install-the-compute-gpu-drivers"></a>Installieren der Compute-GPU-Treiber

Gehen Sie folgendermaßen vor, um Treiber für die Compute-GPU-Größe manuell zu installieren:

1. Wenn Sie im Assistenten für die Lab-Erstellung [Ihr Lab erstellen](./how-to-manage-classroom-labs.md), deaktivieren Sie die Einstellung **GPU-Treiber installieren**.

1. Nachdem das Lab erstellt wurde, stellen Sie eine Verbindung mit der Vorlagen-VM her, um die entsprechenden Treiber zu installieren.

   ![Screenshot der Seite mit den NVIDIA-Treiberdownloads](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Navigieren Sie in einem Browser zur [Seite mit den NVIDIA-Treiberdownloads](https://www.nvidia.com/Download/index.aspx).  
   b. Legen Sie den **Produkttyp** auf **Tesla** fest.  
   c. Legen Sie die **Produktserie** auf **K-Serie** fest.  
   d. Legen Sie das **Betriebssystem** entsprechend dem Typ des Basisimages fest, das Sie beim Erstellen des Labs ausgewählt haben.  
   e. Legen Sie das **CUDA-Toolkit** auf die Version des benötigten CUDA-Treibers fest.  
   f. Wählen Sie **Suchen** aus, um nach Ihren Treibern zu suchen.  
   g. Wählen Sie **Herunterladen** aus, um den Installer herunterzuladen.  
   h. Führen Sie den Installer aus, sodass die Treiber auf der Vorlagen-VM installiert sind.  
1. Überprüfen Sie anhand der Anweisungen im Abschnitt [Überprüfen der installierten Treiber](how-to-setup-lab-gpu.md#validate-the-installed-drivers), ob die Treiber ordnungsgemäß installiert sind. 
1. Nach der Installation der Treiber und anderer Software, die für den Kurs erforderlich ist, wählen Sie **Veröffentlichen** aus, um die VMs Ihrer Kursteilnehmer zu erstellen.

> [!NOTE]
> Wenn Sie ein Linux-Image verwenden, installieren Sie nach dem Herunterladen des Installers die Treiber, indem Sie die Anweisungen unter [Installieren von CUDA-Treibern unter Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms) befolgen.

#### <a name="install-the-visualization-gpu-drivers"></a>Installieren der GPU-Treiber für die Visualisierung

Gehen Sie folgendermaßen vor, um Treiber für die Visualisierungs-GPU-Größe manuell zu installieren:

1. Wenn Sie im Assistenten für die Lab-Erstellung [Ihr Lab erstellen](./how-to-manage-classroom-labs.md), deaktivieren Sie die Einstellung **GPU-Treiber installieren**.
1. Nachdem das Lab erstellt wurde, stellen Sie eine Verbindung mit der Vorlagen-VM her, um die entsprechenden Treiber zu installieren.
1. Installieren Sie die von Microsoft bereitgestellten GRID-Treiber auf der Vorlagen-VM, indem Sie die Anweisungen für Ihr Betriebssystem befolgen:
   -  [Windows-NVIDIA GRID-Treiber](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux-NVIDIA GRID-Treiber](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. Starten Sie die Vorlagen-VM neu.
1. Überprüfen Sie anhand der Anweisungen im Abschnitt [Überprüfen der installierten Treiber](how-to-setup-lab-gpu.md#validate-the-installed-drivers), ob die Treiber ordnungsgemäß installiert sind.
1. Nach der Installation der Treiber und anderer Software, die für den Kurs erforderlich ist, wählen Sie **Veröffentlichen** aus, um die VMs Ihrer Kursteilnehmer zu erstellen.

### <a name="validate-the-installed-drivers"></a>Überprüfen der installierten Treiber
In diesem Abschnitt wird beschrieben, wie Sie überprüfen, ob Ihre GPU-Treiber ordnungsgemäß installiert sind.

#### <a name="windows-images"></a>Windows-Images
1.  Befolgen Sie die Anweisungen im Abschnitt „Überprüfen der Treiberinstallation“ von [Installieren von NVIDIA-GPU-Treibern auf VMs der N-Serie unter Windows](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation).
1.  Wenn Sie eine *Visualisierungs*-GPU verwenden, können Sie auch folgendermaßen vorgehen:
    - Zeigen Sie Ihre GPU-Einstellungen in der NVIDIA-Systemsteuerung an, und passen Sie sie an. Wählen Sie hierzu in der **Windows-Systemsteuerung** die Option **Hardware** aus, und wählen Sie dann die **NVIDIA-Systemsteuerung** aus.

      ![Screenshot der Windows-Systemsteuerung mit dem Link „NVIDIA-Systemsteuerung“](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Zeigen Sie die GPU-Leistung mithilfe des **Task-Managers** an.  Wählen Sie dazu die Registerkarte **Leistung** aus, und wählen Sie dann die Option **GPU** aus.

       ![Screenshot der Registerkarte „GPU-Leistung“ des Task-Managers](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Auf die Einstellungen der NVIDIA-Systemsteuerung kann nur für *Visualisierungs*-GPUs zugegriffen werden.  Wenn Sie versuchen, die NVIDIA-Systemsteuerung für eine Compute-GPU zu öffnen, erhalten Sie die folgende Fehlermeldung: „NVIDIA Display settings are not available.  You are not currently using a display attached to an NVIDIA GPU.“ (NVIDIA-Anzeigeeinstellungen sind nicht verfügbar. Sie verwenden zurzeit keine Anzeige, die an eine NVIDIA-GPU angeschlossen ist.)  Ebenso werden die GPU-Leistungsinformationen im Task-Manager nur für Visualisierungs-GPUs bereitgestellt.

#### <a name="linux-images"></a>Linux-Images
Befolgen Sie die Anweisungen im Abschnitt „Überprüfen der Treiberinstallation“ von [Installieren von NVIDIA-GPU-Treibern auf VMs der N-Serie unter Linux](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen und Verwalten von Labs](how-to-manage-classroom-labs.md)
- [Kurstyp „SOLIDWORKS CAD (Computer-Aided Design)](class-type-solidworks.md)
- [MATLAB-Kurstyp (Matrixlabor)](class-type-matlab.md)