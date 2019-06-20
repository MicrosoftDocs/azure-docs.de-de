---
title: Migrieren von VMs zu Azure Storage Premium | Microsoft-Dokumentation
description: Migrieren Sie Ihre vorhandenen virtuellen Computer zu Azure Storage Premium. Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 5cfb96bd3115c8f3116a28926e93df89dff54351
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153761"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrieren zu Azure Storage Premium (Nicht verwaltete Datenträger)

> [!NOTE]
> In diesem Artikel wird erläutert, wie eine VM mit nicht verwalteten Standarddatenträgern zu einer VM mit nicht verwalteten Premium-Datenträgern migriert wird. Wir empfehlen, Azure Managed Disks für neue virtuelle Computer zu verwenden und bislang nicht verwaltete Datenträger in verwaltete Datenträger umzuwandeln. Das Feature „Managed Disks“ (Verwaltete Datenträger) nimmt Ihnen nämlich die Verwaltung der zugrunde liegenden Speicherkonten ab. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für virtuelle Computer mit E/A-intensiven Workloads. Sie können die VM-Datenträger Ihrer Anwendung zu Azure Storage Premium migrieren, um von der Geschwindigkeit und Leistung dieser Laufwerke zu profitieren.

Der Zweck dieser Anleitung ist es, neue Benutzer von Azure Storage Premium besser auf einen reibungslosen Übergang vom aktuellen System hin zu Storage Premium vorzubereiten. In dieser Anleitung werden drei der Hauptkomponenten bei diesem Vorgang behandelt:

* [Planen der Migration zu Storage Premium](#plan-the-migration-to-premium-storage)
* [Vorbereiten und Kopieren virtueller Festplatten (VHDs) in Storage Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Erstellen eines virtuellen Azure-Computers mit Storage Premium](#create-azure-virtual-machine-using-premium-storage)

Sie können virtuelle Computer von anderen Plattformen zu Azure Storage Premium migrieren oder vorhandene Azure-VMs von Standardspeicher zu Storage Premium migrieren. In dieser Anleitung finden Sie Schritte für beide Szenarien. Befolgen Sie je nach Szenario die im entsprechenden Abschnitt angegebenen Schritte.

> [!NOTE]
> Sie finden eine Übersicht über Features und Preise für Premium-SSDs in: [Auswählen eines Datenträgertyps für virtuelle IaaS-Computer](../../virtual-machines/windows/disks-types.md#premium-ssd). Es wird empfohlen, alle Datenträger von virtuellen Computer, die eine hohe IOPS-Leistung erfordern, zu Azure Storage Premium zu migrieren, um die beste Leistung für Ihre Anwendung zu erzielen. Wenn Ihr Datenträger keine hohe IOPS-Leistung erfordert, können Sie die Kosten beschränken, indem Sie sie im Standard-Speicher belassen. Dieser Speicher speichert Daten auf Datenträgern virtueller Computer auf Festplatten (Hard Disk Drives, HDDs) anstelle von SSDs.
>

Für den Abschluss des vollständigen Migrationsprozesses sind möglicherweise zusätzliche Aktionen vor und nach den in dieser Anleitung angegebenen Schritten erforderlich. Beispiele sind das Konfigurieren von virtuellen Netzwerken oder Endpunkten oder Ändern von Code innerhalb der Anwendung selbst, wofür ggf. in Ihrer Anwendung Ausfallzeiten anfallen. Diese Aktionen sind für jede Anwendung eindeutig. Sie sollten sie zusammen mit den Schritten in dieser Anleitung ausführen, um den kompletten Wechsel zu Storage Premium so reibungslos wie möglich zu gestalten.

## <a name="plan-the-migration-to-premium-storage"></a>Planen der Migration zu Storage Premium
In diesem Abschnitt finden Sie die Voraussetzungen für das Ausführen der in diesem Artikel beschriebenen Migrationsschritte und Hilfe zum Treffen der besten Entscheidung für VM- und Datenträgertypen.

### <a name="prerequisites"></a>Voraussetzungen
* Sie benötigen ein Azure-Abonnement. Wenn Sie über kein Abonnement verfügen, können Sie für einen Monat eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) abonnieren, oder Sie besuchen die Seite mit den [Azure-Preisen](https://azure.microsoft.com/pricing/), die weitere Optionen bietet.
* Zum Ausführen von PowerShell-Cmdlets benötigen Sie das Microsoft Azure PowerShell-Modul. Informationen zum Installationspunkt und zu Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview) .
* Wenn Sie Azure-VMs unter Storage Premium nutzen möchten, müssen Sie Storage Premium-fähige VMs verwenden. Mit Storage Premium-fähigen virtuellen Computern können Sie Standard- und Storage Premium-Datenträger verwenden. Premium-Datenträger werden zukünftig mit mehreren VM-Typen verfügbar sein. Weitere Informationen zu den verfügbaren Typen und Größen von Azure-VM-Datenträgern finden Sie unter [Größen für virtuelle Computer in Azure](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) und [Größen für Clouddienste](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Überlegungen
Ein virtueller Azure-Computer unterstützt das Anfügen mehrerer Storage Premium-Datenträger, damit Ihre Anwendung bis zu 256 TB Speicher pro virtuellem Computer nutzen kann. Mit Storage Premium können Ihre Anwendungen bis zu 80.000 IOPS (Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde) pro virtuellem Computer nutzen sowie 2.000 MB Datenträgerdurchsatz pro Sekunde und virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen. Sie haben die Wahl zwischen einer Vielzahl virtueller Computer und Datenträger. Anhand der Informationen in diesem Abschnitt können Sie die Ihrer Workload am besten entsprechende Option finden.

#### <a name="vm-sizes"></a>VM-Größen
Die Größenspezifikationen der Azure-VM sind unter [Größen für virtuelle Computer](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)aufgelistet. Sehen Sie sich die Leistungsmerkmale von virtuellen Computern für Storage Premium an, und wählen Sie die am besten für Ihre Workload geeignete VM-Größe aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

#### <a name="disk-sizes"></a>Datenträgergrößen
Es gibt fünf Datenträgertypen, die Sie mit Ihrem virtuellen Computer verwenden können. Jeder Typ weist bestimmte IOPS- und Durchsatzeinschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

| Premium-Datenträgertyp  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Datenträgergröße           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS pro Datenträger       | 500   | 2\.300  | 5\.000           | 7\.500           | 7\.500           | 
| Durchsatz pro Datenträger | 100 MB pro Sekunde | 150 MB pro Sekunde | 200 MB pro Sekunde | 250 MB pro Sekunde | 250 MB pro Sekunde |

Legen Sie je nach Workload fest, ob zusätzliche Datenträger für Ihren virtuellen Computer erforderlich sind. Sie können mehrere Datenträger für permanente Daten auf Ihrem virtuellen Computer anfügen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen. ([Hier](../../virtual-machines/windows/premium-storage-performance.md#disk-striping) erfahren Sie, was Datenträgerstriping ist.) Wenn Sie Daten über Storage Premium-Datenträger mithilfe von [Speicherplätzen][4] verteilen, sollten Sie sie für jeden verwendeten Datenträger eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolumes aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Für Linux-VMs können Sie dazu das Hilfsprogramm *mdadm* verwenden. Weitere Informationen finden Sie im Artikel [Konfigurieren von Software-RAID unter Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

#### <a name="storage-account-scalability-targets"></a>Skalierbarkeitsziele für das Speicherkonto
Storage Premium-Konten haben zusätzlich zu den unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](storage-scalability-targets.md) aufgeführten Zielen folgende Skalierbarkeitsziele. Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten.

| Gesamtkapazität des Kontos | Gesamtbandbreite für ein lokal redundantes Speicherkonto |
|:--- |:--- |
| Datenträgerkapazität: 35 TB<br />Kapazität für Momentaufnahmen: 10 TB |Bis zu 50 GB pro Sekunde für eingehenden und ausgehenden Datenverkehr |

Weitere Informationen zu den Spezifikationen für Storage Premium finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md#premium-performance-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Zwischenspeicherungsrichtlinie für Datenträger
Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können. Die Einstellungen für das Zwischenspeichern bei vorhandenen Datenträgern können Sie über das [Azure-Portal](https://portal.azure.com) oder den Parameter *-HostCaching* des Cmdlets *Set-AzureDataDisk* aktualisieren.

#### <a name="location"></a>Location
Wählen Sie einen Speicherort, an dem Azure Premium-Speicher verfügbar ist. Aktuelle Informationen zu verfügbaren Standorten finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services). Virtuelle Computer in der gleichen Umgebung wie das Speicherkonto, in dem die Datenträger des virtuellen Computers gespeichert sind, liefern eine wesentlich höhere Leistung als solche in unterschiedlichen Regionen.

#### <a name="other-azure-vm-configuration-settings"></a>Sonstige Azure-VM-Konfigurationseinstellungen
Beim Erstellen einer Azure-VM müssen Sie bestimmte Einstellungen für den virtuellen Computer konfigurieren. Denken Sie daran, dass einige Einstellungen für die Lebensdauer des virtuellen Computers festgelegt sind, während Sie andere Einstellungen später hinzufügen oder ändern können. Überprüfen Sie diese Azure-VM-Konfigurationseinstellungen, und stellen Sie sicher, dass diese korrekt konfiguriert sind, um die Anforderungen für die Workload zu erfüllen.

### <a name="optimization"></a>Optimierung
[Azure Storage Premium: Entwurf für hohe Leistung](../../virtual-machines/windows/premium-storage-performance.md): Dieser Artikel bietet Leitfäden zum Erstellen leistungsstarker Anwendungen mit Azure Storage Premium. Sie können die Leitlinien kombiniert mit den bewährten Methoden für hohe Leistung befolgen, die für von Ihrer Anwendung verwendeten Technologien gelten.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Vorbereiten und Kopieren virtueller Festplatten (VHDs) in Storage Premium
Der folgende Abschnitt enthält Leitlinien für das Vorbereiten der virtuellen Festplatten Ihres virtuellen Computers und Kopieren von VHDs in Azure Storage.

* [Szenario 1: Migrieren vorhandener Azure-VMs zu Azure Storage Premium](#scenario1).
* [Szenario 2: Migrieren von VMs von anderen Plattformen zu Azure Storage Premium](#scenario2).

### <a name="prerequisites"></a>Voraussetzungen
Um die virtuelle Festplatten (VHDs) für die Migration vorzubereiten, benötigen Sie Folgendes:

* Ein Azure-Abonnement, ein Speicherkonto und einen Container im Speicherkonto, in den die virtuelle Festplatte kopiert werden soll. Beachten Sie, dass das Zielspeicherkonto je nach Ihren Anforderung ein Standard- oder Premium-Speicherkonto sein kann.
* Ein Tool zum Generalisieren der VHD, wenn Sie mehrere VM-Instanzen daraus erstellen möchten. Beispiel: Sysprep für Windows oder virt-sysprep für Ubuntu.
* Ein Tool für den Upload der VHD-Datei in das Speicherkonto. Weitere Informationen finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md), oder verwenden Sie einen [Azure Storage-Explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). In dieser Anleitung wird erläutert, wie Sie Ihre VHD mit dem Tool AzCopy kopieren.

> [!NOTE]
> Bei Wahl der synchronen Kopieroption mit AzCopy kopieren Sie die VHD im Hinblick auf eine optimale Leistung, indem Sie eines dieser Tools auf einem virtuellen Azure-Computer ausführen, der sich in der gleichen Region wie das Zielspeicherkonto befindet. Wenn Sie eine VDH von einem virtuellen Azure-Computer in einer anderen Region kopieren, kann dies die Leistung beeinträchtigen.
>
> Zum Kopieren großer Datenmengen bei begrenzter Bandbreite könnten Sie auch den [Microsoft Azure Import/Export-Dienst zum Übertragen von Daten nach Blob Storage](../storage-import-export-service.md)nutzen, um Daten durch den Versand von Festplatten an ein Azure-Rechenzentrum zu übertragen. Mit dem Azure Import/Export-Dienst können Sie Daten nur in ein Standardspeicherkonto kopieren. Sobald sich die Daten im Standardspeicherkonto befinden, können Sie sie mit der [Copy Blob-API](https://msdn.microsoft.com/library/azure/dd894037.aspx) oder mit AzCopy in Ihr Storage Premium-Konto übertragen.
>
> Beachten Sie, dass Microsoft Azure nur VHD-Dateien mit fester Größe unterstützt. VHDX-Dateien und dynamische virtuelle Festplatten werden nicht unterstützt. Wenn Sie über eine dynamische virtuelle Festplatte verfügen, können Sie sie mit dem Cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) in eine VHD mit fester Größe konvertieren .
>
>

### <a name="scenario1"></a>Szenario 1: Migrieren vorhandener Azure-VMs zu Azure Storage Premium.
Wenn Sie vorhandene Azure-VMs migrieren, halten Sie die VM an, bereiten VHDs gemäß dem gewünschten VHD-Typ vor und kopieren dann die VHD mit AzCopy oder PowerShell.

Die VM muss vollständig heruntergefahren sein, um einen fehlerfreien Zustand zu migrieren. Die Ausfallzeit hält bis zum Abschluss der Migration an.

#### <a name="step-1-prepare-vhds-for-migration"></a>Schritt 1: Vorbereiten von VHDs für die Migration
Wenn Sie vorhandene Azure-VMs zu Storage Premium migrieren, kann die virtuelle Festplatte Folgendes sein:

* Ein generalisiertes Betriebssystemimage
* Ein eindeutiger Betriebssystem-Datenträger
* Ein Datenträger

Im Folgenden werden diese drei Szenarien für die Vorbereitung Ihrer virtuellen Festplatte erörtert.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Verwenden einer generalisierten Betriebssystem-VHD zum Erstellen mehrerer VM-Instanzen
Denken Sie beim Hochladen einer VHD-Datei, mit der mehrere generische Azure-VM-Instanzen erstellt werden, daran, dass Sie die VHD zunächst mit einem Dienstprogramm zur Systemvorbereitung (Sysprep) generalisieren müssen. Dies gilt für eine lokale virtuelle Festplatte ebenso wie für Cloud-Festplatten. Mit Sysprep werden alle computerspezifischen Informationen von der virtuellen Festplatte entfernt.

> [!IMPORTANT]
> Erstellen Sie eine Momentaufnahme oder eine Sicherung Ihres virtuellen Computers, bevor Sie ihn generalisieren. Das Ausführen von sysprep wird beendet und die VM-Instanz freigeben. Führen Sie die Schritte unten zur Vorbereitung einer VHD mit Windows-Betriebssystem aus. Beachten Sie, dass beim Ausführen des Sysprep-Befehls der virtuelle Computer heruntergefahren werden muss. Weitere Informationen zu Sysprep finden Sie unter [Sysprep (Systemvorbereitung) – Übersicht](https://technet.microsoft.com/library/hh825209.aspx) oder unter [Technische Referenz zu Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.
2. Geben Sie den folgenden Befehl zum Öffnen von Sysprep ein:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Wählen Sie im Systemvorbereitungsprogramm die Option „Out-of-Box-Experience (OOBE) für System aktivieren“, aktivieren Sie das Kontrollkästchen „Verallgemeinern“, wählen Sie **Herunterfahren**, und klicken Sie dann auf **OK** (siehe dazu Abbildung unten). Dadurch wird das Betriebssystem generalisiert und das System heruntergefahren.

    ![][1]

Verwenden Sie für einen virtuellen Ubuntu-Computer "virt-sysprep". Weitere Informationen finden Sie unter [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) (in englischer Sprache). Für diese Vorgänge steht auch [Open-Source-Software zur Linux-Serverbereitstellung](https://www.cyberciti.biz/tips/server-provisioning-software.html) für andere Linux-Betriebssysteme zur Verfügung.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Verwenden einer eindeutigen Betriebssystem-VHD zum Erstellen einer einzelnen VM-Instanz
Wenn auf dem virtuellen Computer eine Anwendung ausgeführt wird, die die spezifischen Daten des Computers benötigt, generalisieren Sie die virtuelle Festplatte nicht. Eine nicht generalisierte virtuelle Festplatte kann verwendet werden, um eine eindeutige Azure-VM-Instanz zu erstellen. Wenn Sie beispielsweise über einen Domänencontroller für die virtuelle Festplatte verfügen, wird dieser durch die Ausführung von Sysprep außer Kraft gesetzt. Überprüfen Sie die auf dem virtuellen Computer ausgeführten Anwendungen und die Auswirkung der Ausführung von Sysprep auf diese, bevor Sie die VHD generalisieren.

##### <a name="register-data-disk-vhd"></a>Registrieren einer Datenträger-VHD
Wenn Sie über Datenträger in Azure verfügen, die migriert werden sollen, müssen Sie sicherstellen, dass die VMs, die diese Datenträger nutzen, heruntergefahren sind.

Folgen Sie den nachstehenden Schritten zum Kopieren einer VHD in Azure Storage Premium und ihrer Registrierung als bereitgestellter Datenträger.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Schritt 2: Erstellen des Ziels für die virtuelle Festplatte
Erstellen Sie ein Speicherkonto für die Verwaltung Ihrer virtuellen Festplatten. Berücksichtigen Sie die folgenden Punkte beim Planen des Speicherorts Ihrer virtuellen Festplatten:

* Das Storage Premium-Zielkonto.
* Der Speicherort des Kontos muss mit dem der Storage Premium-fähigen Azure-VMs übereinstimmen, die Sie in der letzten Phase erstellen. Sie können je nach Anforderungen in ein neues Speicherkonto kopieren oder die Verwendung desselben Speicherkontos planen.
* Kopieren und speichern Sie den Speicherkontenschlüssel des Zielspeicherkontos für die nächste Phase.

Für Datenträger können Sie auswählen, dass einige in einem Standardspeicherkonto verbleiben (z.B. Datenträger, auf die weniger zugegriffen wird). Wir empfehlen jedoch ausdrücklich, alle Daten für Produktionsworkloads in Storage Premium zu verschieben.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Schritt 3: Kopieren der VHD mit AzCopy oder mit PowerShell
Sie müssen Ihren Containerpfad und Speicherkontoschlüssel kennen, um eine dieser beiden Optionen nutzen zu können. Den Containerpfad und Speicherkontoschlüssel finden Sie hier: **Azure-Portal** > **Speicher**. Die Container-URL lautet beispielsweise „https:\//myaccount.blob.core.windows.net/meincontainer/“.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1: Kopieren einer VHD mit AzCopy (asynchrone Kopie)
Mit AzCopy können Sie die VHD auf einfache Weise über das Internet hochladen. Je nach Größe der virtuellen Festplatten kann dies einige Zeit in Anspruch nehmen. Denken Sie daran, die Eingangs-/Ausgangsgrenzen des Speicherkontos bei Verwendung dieser Option zu überprüfen. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md) .

1. Laden Sie AzCopy hier herunter, und installieren Sie das Befehlszeilenprogramm: [Neueste Version von AzCopy](https://aka.ms/downloadazcopy)
2. Öffnen Sie Azure PowerShell, und wechseln Sie zu dem Ordner, in dem AzCopy installiert ist.
3. Verwenden Sie den folgenden Befehl, um die VHD-Datei von der "Quelle" zum "Ziel" zu kopieren.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Beispiel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Es folgt die Beschreibung der im AzCopy-Befehl verwendeten Parameter:

   * **/Source: *&lt;Quelle&gt;:* ** Speicherort der Ordner- oder Speichercontainer-URL, die die VHD enthält.
   * **/SourceKey: *&lt;source-account-key&gt;:* ** Speicherkontoschlüssel des Quellspeicherkontos.
   * **/Dest: *&lt;Ziel&gt;:* ** Speichercontainer-URL, in die die VHD kopiert werden soll.
   * **/DestKey: *&lt;dest-account-key&gt;:* ** Speicherkontoschlüssel des Zielspeicherkontos.
   * **/Pattern: *&lt;file-name&gt;:* ** Geben Sie den Dateinamen der zu kopierenden VHD-Datei an.

Details zur Verwendung des Tools AzCopy finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2: Kopieren einer VHD mit PowerShell (synchronisierte Kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sie können die VHD-Datei auch mithilfe des PowerShell-Cmdlets „Start-AzStorageBlobCopy“ kopieren. Verwenden Sie zum Kopieren der VHD den folgenden Befehl in Azure PowerShell: Ersetzen Sie die Werte in <> mit den entsprechenden Werten aus Ihrem Quell- und Zielspeicherkonto. Um diesen Befehl zu verwenden, müssen Sie im Zielspeicherkonto über einen Container namens "vhds" verfügen. Wenn der Container nicht vorhanden ist, erstellen Sie einen vor dem Ausführen des Befehls.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Beispiel:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Szenario 2: Migrieren von VMs von anderen Plattformen zu Azure Storage Premium.
Wenn Sie virtuelle Festplatten aus anderen Cloud-Speichern als Azure zu Azure migrieren möchten, müssen Sie zuerst die VHD-Datei in ein lokales Verzeichnis exportieren. Ermitteln Sie den vollständigen Quellpfad des lokalen Verzeichnisses, in dem die VHD gespeichert ist, und laden Sie sie dann mit AzCopy in Azure Storage hoch.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Schritt 1: Exportieren der VHD in ein lokales Verzeichnis
##### <a name="copy-a-vhd-from-aws"></a>Kopieren einer VHD aus AWS
1. Bei Verwendung von AWS exportieren Sie die EC2-Instanz in eine virtuelle Festplatte in einem Amazon S3-Bucket. Befolgen Sie die in der Amazon-Dokumentation unter „Exporting Amazon EC2 Instances (Exportieren von Amazon EC2-Instanzen)“ beschriebenen Schritte, um die Amazon EC2-CLI (Befehlszeilenschnittstelle) zu installieren, und führen Sie den Befehl „create-instance-export-task“ zum Exportieren der EC2-Instanz in eine VHD-Datei aus. Verwenden Sie für die Variable „DISK&#95;IMAGE&#95;FORMAT“ unbedingt **VHD**, wenn Sie den Befehl **create-instance-export-task** ausführen. Die exportierte VHD-Datei wird im Amazon S3-Bucket gespeichert, der während dieses Prozesses angegeben wurde.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Laden Sie die VHD-Datei aus dem S3-Bucket herunter. Wählen Sie die VHD-Datei und dann **Aktionen** > **Herunterladen** aus.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Kopieren einer VHD aus einer anderen Cloud als Azure
Wenn Sie virtuelle Festplatten aus anderen Cloud-Speichern als Azure zu Azure migrieren möchten, müssen Sie zuerst die VHD-Datei in ein lokales Verzeichnis exportieren. Kopieren Sie den vollständigen Quellpfad des lokalen Verzeichnisses, in dem die VHD-Datei gespeichert ist.

##### <a name="copy-a-vhd-from-on-premises"></a>Kopieren einer VHD aus einer lokalen Umgebung
Wenn Sie die VHD aus einer lokalen Umgebung migrieren, benötigen Sie den vollständigen Quellpfad, in dem die VHD gespeichert ist. Der Quellpfad kann ein Serverspeicherort oder eine Dateifreigabe sein.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Schritt 2: Erstellen des Ziels für die virtuelle Festplatte
Erstellen Sie ein Speicherkonto für die Verwaltung Ihrer virtuellen Festplatten. Berücksichtigen Sie die folgenden Punkte beim Planen des Speicherorts Ihrer virtuellen Festplatten:

* Der Speicherort des Zielspeicherkontos kann sich je nach Anwendungsanforderungen in einem Standard- oder Premium-Speicher befinden.
* Die Region des Speicherkontos muss mit der der Storage Premium-fähigen Azure-VMs übereinstimmen, die Sie in der letzten Phase erstellen. Sie können je nach Anforderungen in ein neues Speicherkonto kopieren oder die Verwendung desselben Speicherkontos planen.
* Kopieren und speichern Sie den Speicherkontenschlüssel des Zielspeicherkontos für die nächste Phase.

Wir empfehlen dringend, alle Daten für die Produktionsworkload in Storage Premium zu verschieben.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Schritt 3: Hochladen der VHD in Azure Storage
Nachdem Sie Ihre VHD in das lokale Verzeichnis verschoben haben, können Sie die VHD-Datei mit AzCopy oder Azure PowerShell in Azure Storage hochladen. Beide Optionen werden hier beschrieben:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1: Verwenden des Azure PowerShell-Cmdlets „Add-AzureVhd“ zum Hochladen der VHD-Datei

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Ein Beispiel für \<Uri> ist ***„https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd“***. Ein Beispiel für \<FileInfo> ist ***„C:\Pfad\zu\upload.vhd“***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2: Verwenden von AzCopy zum Hochladen der VHD-Datei
Mit AzCopy können Sie die VHD auf einfache Weise über das Internet hochladen. Je nach Größe der virtuellen Festplatten kann dies einige Zeit in Anspruch nehmen. Denken Sie daran, die Eingangs-/Ausgangsgrenzen des Speicherkontos bei Verwendung dieser Option zu überprüfen. Ausführliche Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage-scalability-targets.md) .

1. Laden Sie AzCopy hier herunter, und installieren Sie das Befehlszeilenprogramm: [Neueste Version von AzCopy](https://aka.ms/downloadazcopy)
2. Öffnen Sie Azure PowerShell, und wechseln Sie zu dem Ordner, in dem AzCopy installiert ist.
3. Verwenden Sie den folgenden Befehl, um die VHD-Datei von der "Quelle" zum "Ziel" zu kopieren.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Beispiel:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Es folgt die Beschreibung der im AzCopy-Befehl verwendeten Parameter:

   * **/Source: *&lt;Quelle&gt;:* ** Speicherort der Ordner- oder Speichercontainer-URL, die die VHD enthält.
   * **/SourceKey: *&lt;source-account-key&gt;:* ** Speicherkontoschlüssel des Quellspeicherkontos.
   * **/Dest: *&lt;Ziel&gt;:* ** Speichercontainer-URL, in die die VHD kopiert werden soll.
   * **/DestKey: *&lt;dest-account-key&gt;:* ** Speicherkontoschlüssel des Zielspeicherkontos.
   * **/BlobType: page:** Gibt an, dass das Ziel ein Seitenblob ist.
   * **/Pattern: *&lt;file-name&gt;:* ** Geben Sie den Dateinamen der zu kopierenden VHD-Datei an.

Details zur Verwendung des Tools AzCopy finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Weitere Optionen zum Hochladen einer virtuellen Festplatte
Sie können virtuelle Festplatten zudem mit den folgenden Tools in ein Speicherkonto hochladen:

* [Azure Storage Copy Blob-API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage-Explorer: Hochladen von Blobs](https://azurestorageexplorer.codeplex.com/)
* [Speicher-Import/Export Service REST-API-Referenz](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Wir empfehlen das Verwenden des Import/Export-Diensts, wenn die geschätzte Hochladedauer sieben Tage überschreitet. Sie können mithilfe von [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) die Dauer anhand der Datengröße und Übertragungseinheit schätzen.
>
> Der Import/Export-Dienst kann zum Kopieren in oder aus dem Standardspeicherkonto verwendet werden. Sie benötigen ein Tool wie AzCopy zum Kopieren zwischen Standardspeicherkonto und Premium-Speicherkonto.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Erstellen von Azure-VMs mit Storage Premium
Nachdem die virtuelle Festplatte in das gewünschte Speicherkonto hochgeladen oder kopiert wurde, führen Sie die Anweisungen in diesem Abschnitt aus, um die virtuelle Festplatte je nach Szenario als Betriebssystemimage oder Betriebssystem-Datenträger zu registrieren und anschließend eine VM-Instanz daraus zu erstellen. Das VHD-Datenlaufwerk kann dem virtuellen Computer hinzugefügt werden, nachdem es erstellt wurde.
Ein Beispielskript für die Migration wird am Ende dieses Abschnitts bereitgestellt. Dieses einfache Skript eignet sich nicht für alle Szenarien. Sie müssen womöglich das Skript dem speziellen Szenario entsprechend aktualisieren. Um festzustellen, ob das Skript für Ihr Szenario passt, lesen Sie weiter unten [Ein Beispielskript für die Migration](#a-sample-migration-script).

### <a name="checklist"></a>Checkliste
1. Warten Sie, bis das Kopieren aller VHD-Datenträger abgeschlossen ist.
2. Stellen Sie sicher, dass Storage Premium in der Region verfügbar ist, in die migriert werden soll.
3. Bestimmen Sie die neue VM-Serie, die Sie verwenden möchten. Sie muss Storage Premium-fähig sein und ihre Größe von der Verfügbarkeit in der Region und Ihren Anforderungen abhängig sein.
4. Bestimmen Sie die genaue VM-Größe, die Sie verwenden möchten. Die VM-Größe muss groß genug sein, um die Anzahl der vorhandenen Datenträger zu unterstützen. Beispiel: Wenn Sie über vier Datenträger verfügen, muss der virtuelle Computer mindestens zwei Kerne haben. Berücksichtigen Sie auch die Anforderungen hinsichtlich Verarbeitungsleistung, Arbeitsspeicher und Netzwerkbandbreite.
5. Erstellen Sie ein Storage Premium-Konto in der Zielregion. Dies ist das Konto, das Sie für den neuen virtuellen Computer verwenden werden.
6. Halten Sie die Details zum aktuellen virtuellen Computer bereit, einschließlich der Liste der Datenträger und die zugehörigen VHD-Blobs.

Bereiten Sie Ihre Anwendung für Ausfallzeiten vor. Für eine einwandfreie Migration müssen Sie die gesamte Verarbeitung im aktuellen System beenden. Erst dann können Sie den konsistenten Zustand erreichen, den Sie auf die neue Plattform migrieren können. Die Dauer der Ausfallzeit hängt von der Datenmenge der zu migrierenden Datenträger ab.

> [!NOTE]
> Wenn Sie eine Azure Resource Manager-VM anhand eines speziellen VHD-Datenträger erstellen, lesen Sie die Informationen zu [dieser Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) für die Bereitstellung einer Resource Manager-VM mithilfe eines vorhandenen Datenträgers.
>
>

### <a name="register-your-vhd"></a>Registrieren der virtuellen Festplatte
Zum Erstellen eines virtuellen Computers anhand einer Betriebssystem-VHD oder zum Hinzufügen eines Datenträgers zu einem neuen virtuellen Computer müssen Sie diese zunächst registrieren. Führen Sie je nach VHD-Szenario die nachstehenden Schritte aus.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen
Nachdem das generalisierte Betriebssystemimage in das VHD-Speicherkonto hochgeladen wurde, registrieren Sie es als **Azure-VM-Image**, sodass Sie damit VM-Instanzen erstellen können. Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemimage zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopieren und speichern Sie den Namen des neuen Azure-VM-Images. In dem Beispiel oben ist dies *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz
Nachdem die eindeutige Betriebssystem-VHD in das Speicherkonto hochgeladen wurde, registrieren Sie sie als **Azure-Betriebssystem-Datenträger**, damit Sie eine VM-Instanz davon erstellen können. Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-VM-Betriebssystemdatenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopieren und speichern Sie den Namen des neuen Azure-Betriebssystemdatenträger. In dem Beispiel oben ist dies *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Neuen Azure-VM-Instanzen zuzuordnende Datenträger-VHD
Nachdem die Datenträger-VHD in das Speicherkonto hochgeladen wurde, registrieren Sie sie als Azure-Datenträger, sodass sie an die neue Azure-VM-Instanz der DS-, DSv2- oder GS-Serie angefügt werden kann.

Verwenden Sie die folgenden PowerShell-Cmdlets, um die virtuelle Festplatte als Azure-Datenträger zu registrieren. Geben Sie die vollständige Container-URL an, an die die virtuelle Festplatte kopiert wurde.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopieren und speichern Sie den Namen des neuen Azure-Datenträgers. In dem Beispiel oben ist dies *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Erstellen einer Storage Premium-fähigen VM
Nachdem das Betriebssystemimage oder der Betriebssystemdatenträger registriert wurde, können Sie einen neuen virtuellen Computer der DS-, DSv2- oder GS-Serie erstellen. Sie verwenden dazu den Namen des Betriebssystemimages oder Betriebssystemdatenträgers, den Sie registriert haben. Wählen Sie den virtuellen Computer aus der Premium-Speicherebene aus. Im folgenden Beispiel wird die VM-Größe *Standard_DS2* verwendet.

> [!NOTE]
> Aktualisieren Sie die Größe des Datenträgers, um sicherzustellen, dass er Ihren Anforderungen an Kapazität und Leistung sowie den Azure-Datenträgergrößen entspricht.
>
>

Führen Sie nacheinander die folgenden PowerShell-Cmdlets aus, um den neuen virtuellen Computer zu erstellen. Legen Sie zunächst die allgemeinen Parameter fest:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Erstellen Sie zuerst einen Clouddienst, in dem die neuen virtuellen Computer gehostet werden.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Erstellen Sie anschließend je nach Szenario die Azure-VM-Instanz aus dem registrierten Betriebssystemimage oder Betriebssystemdatenträger.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalisierte Betriebssystem-VHD zum Erstellen mehrerer Azure-VM-Instanzen
Erstellen Sie eine oder mehrere neue Azure-VM-Instanzen der DS-Serie mit dem **Azure-Betriebssystemimage** , das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemimages in der Konfiguration des virtuellen Computers an, wie dies unten dargestellt ist.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Eindeutige Betriebssystem-VHD zum Erstellen einer einzelnen Azure-VM-Instanz
Erstellen Sie eine neue Azure-VM-Instanz der DS-Serie mit dem **Azure-Betriebssystemimage** , das Sie registriert haben. Geben Sie beim Erstellen eines neuen virtuellen Computers den Namen des Betriebssystemdatenträgers in der Konfiguration des virtuellen Computers an, wie dies unten dargestellt ist.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Geben Sie andere Azure-VM-Informationen an, z. B. Clouddienst, Region, Speicherkonto, Verfügbarkeitssatz und Zwischenspeicherungsrichtlinie. Beachten Sie, dass die VM-Instanz mit dem zugehörigen Betriebssystem oder Datenträger angeordnet werden muss, d.h., dass alle ausgewählten Clouddienste, Regionen und Speicherkonten sich an demselben Speicherort befinden müssen wie die zugrunde liegenden VHDs dieser Datenträger.

### <a name="attach-data-disk"></a>Datenträger anfügen
Wenn Sie Datenträger-VHDs registriert haben, fügen Sie sie im letzten Schritt an die neue Storage Premium-fähige Azure-VM an.

Verwenden Sie das folgende PowerShell-Cmdlet, um Datenträger an den neuen virtuellen Computer anzufügen, und geben Sie die Zwischenspeicherungsrichtlinie an. Im Beispiel unten wird die Zwischenspeicherungsrichtlinie als schreibgeschützt ( *ReadOnly*) festgelegt.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Möglicherweise sind weitere Schritte zur Unterstützung Ihrer Anwendung erforderlich, die in dieser Anleitung nicht behandelt werden.
>
>

### <a name="checking-and-plan-backup"></a>Überprüfen und Planen der Sicherung
Sobald der neue virtuelle Computer ausgeführt wird, können Sie mit demselben Benutzernamen und Kennwort wie für den ursprünglichen virtuellen Computer darauf zugreifen und überprüfen, ob alles wie erwartet funktioniert. Alle Einstellungen, einschließlich der Stripesetvolumes, sind auch im neuen virtuellen Computer vorhanden.

Der letzte Schritt besteht darin, den Sicherungs- und Wartungszeitplan für den neuen virtuellen Computer auf Grundlage der Anwendungsanforderungen zu planen.

### <a name="a-sample-migration-script"></a>Ein Beispielskript für die Migration
Wenn Sie mehrere zu migrierende virtuelle Computer haben, kann eine Automatisierung mithilfe von PowerShell-Skripts hilfreich sein. Im Folgenden finden Sie ein Beispielskript, das die Migration eines virtuellen Computers automatisiert. Beachten Sie, dass das unten stehende Skript nur ein Beispiel ist, und dass es nur einige Annahmen über die aktuellen VM-Datenträger gibt. Sie müssen womöglich das Skript dem speziellen Szenario entsprechend aktualisieren.

Die Annahmen sind:

* Sie erstellen klassischen Azure-VMs.
* Ihre Quellbetriebssystem-Datenträger und Quelldatenträger befinden sich im selben Speicherkonto und Container. Wenn sich die Betriebssystem-Datenträger und anderen Datenträger nicht am selben Speicherort befinden, können Sie AzCopy oder Azure PowerShell verwenden, um VHDs in Speicherkonten und Container zu kopieren. Weitere Informationen finden Sie im vorherigen Schritt: [Kopieren der VHD mit AzCopy oder mit PowerShell](#copy-vhd-with-azcopy-or-powershell). Das Bearbeiten dieses Skripts für Ihr Szenario ist eine andere Option. Doch wir empfehlen die Verwendung von AzCopy oder PowerShell, da dies einfacher und schneller ist.

Das Automatisierungsskript wird unten bereitgestellt. Ersetzen Sie Text durch Ihre Informationen, und aktualisieren Sie das Skript entsprechend Ihrem jeweiligen Szenario.

> [!NOTE]
> Bei Verwenden des vorhandenen Skripts wird die Netzwerkkonfiguration Ihrer Quell-VM nicht beibehalten. Sie müssen die Netzwerkeinstellungen in Ihren migrierten VMs neu konfigurieren.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimierung
Die aktuelle Konfiguration des virtuellen Computers kann speziell für die Arbeit mit Standarddatenträgern angepasst werden, zum Beispiel, um die Leistung zu erhöhen. Verwenden Sie dazu viele Datenträger in einem Stripesetvolume. Beispielsweise können Sie, statt 4 Datenträger separat unter Storage Premium zu verwenden, die Kosten optimieren, indem Sie einen einzelnen Datenträger verwenden. Optimierungen wie diese müssen von Fall zu Fall erfolgen und erfordern benutzerdefinierte Schritte nach der Migration. Beachten Sie auch, dass dieser Prozess bei Datenbanken und Anwendungen, die von dem während der Einrichtung definierten Datenträgerlayout abhängig sind, möglicherweise nicht gut funktioniert.

##### <a name="preparation"></a>Vorbereitung
1. Führen Sie die einfache Migration wie im vorigen Abschnitt beschrieben durch. Optimierungen werden nach der Migration auf dem neuen virtuellen Computer ausgeführt.
2. Definieren Sie die neue Datenträgergröße, die für eine optimale Konfiguration erforderlich ist.
3. Bestimmen Sie die Zuordnung der aktuellen Datenträger/Volumes zu neuen Datenträgerspezifikationen.

##### <a name="execution-steps"></a>Ausführungsschritte
1. Erstellen Sie die neue Datenträger mit der richtigen Größe auf dem virtuellen Storage Premium-Computer.
2. Melden Sie sich beim virtuellen Computer an, und kopieren Sie die Daten vom aktuellen Volume auf den neuen Datenträger, der diesem Volume zugeordnet ist. Führen Sie diesen Vorgang für alle aktuellen Volumes durch, die einer neuen Festplatte zugeordnet werden müssen.
3. Ändern Sie als Nächstes die Anwendungseinstellungen, um zu den neuen Datenträgern zu wechseln, und trennen Sie die alten Volumes.

Informationen zur Optimierung der Anwendung für eine bessere Datenträgerleistung finden Sie in unserem Artikel [Entwerfen für hohe Leistung](../../virtual-machines/windows/premium-storage-performance.md) im Abschnitt „Optimieren der Anwendungsleistung“.

### <a name="application-migrations"></a>Anwendungsmigrationen
Datenbanken und andere komplexe Anwendungen erfordern womöglich spezielle Schritte, wie vom Anwendungsanbieter für die Migration definiert. Weitere Informationen finden Sie in der Dokumentation der jeweiligen Anwendung. Beispiel: So können Datenbanken in der Regel mittels Sicherung und Wiederherstellung migriert werden.

## <a name="next-steps"></a>Nächste Schritte
Informationen zu bestimmten Szenarios zur Migration virtueller Computer finden Sie in den folgenden Ressourcen:

* [Migrate Azure Virtual Machines between Storage Accounts (in englischer Sprache)](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Erstellen und Hochladen einer Windows Server-VHD nach Azure](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Erstellen und Hochladen einer Linux-VHD in Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrating Virtual Machines from Amazon AWS to Microsoft Azure (in englischer Sprache)](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Lesen Sie außerdem die folgenden Ressourcen, um mehr über Azure Storage und Azure Virtual Machines zu erfahren:

* [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/documentation/services/storage/)
* [Dokumentation zu virtuellen Computern](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Auswählen eines Datenträgertyps für virtuelle IaaS-Computer](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
