---
title: Leistungsrichtlinien für SQL Server in Azure | Microsoft-Dokumentation
description: Dieses Thema enthält Richtlinien zur Optimierung der Leistung von SQL Server in Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3fda34e46ddb7ea17c98795ad6632841b79764eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076914"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Leistungsrichtlinien für SQL Server in Azure Virtual Machines

## <a name="overview"></a>Übersicht

Dieser Artikel enthält Informationen zur Optimierung der Leistung von SQL Server auf virtuellen Microsoft Azure-Computern. Es empfiehlt sich, beim Ausführen von SQL Server in Azure Virtual Machines weiterhin die gleichen Optionen zur Optimierung der Datenbankleistung zu verwenden, die für SQL Server in der lokalen Server-Umgebung gelten. Die Leistung einer relationalen Datenbank in einer öffentlichen Cloud hängt jedoch von vielen Faktoren ab, z. B. der Größe eines virtuellen Computers und der Konfiguration der Datenträger für Daten.

[Im Azure-Portal bereitgestellte SQL Server-Images ](quickstart-sql-vm-create-portal.md) befolgen die bewährten Methoden für die allgemeine Speicherkonfiguration (weitere Informationen zum Konfigurieren von Speicher finden Sie unter [Speicherkonfiguration für SQL Server-VMs](virtual-machines-windows-sql-server-storage-configuration.md)). Nach der Bereitstellung empfiehlt es sich gegebenenfalls, weitere der in diesem Artikel behandelten Optimierungen anzuwenden. Orientieren Sie sich dabei an Ihrer Workload, und testen Sie die gewählte Option.

> [!TIP]
> In der Regel kommt es zu einem Kompromiss zwischen einer Kostenoptimierung und einer Leistungsoptimierung. Dieser Artikel konzentriert sich auf die *optimale* Leistung von SQL Server auf Azure-VMs. Wenn Ihre Workload weniger anspruchsvoll ist, sind möglicherweise nicht alle unten aufgeführten Optimierungen erforderlich. Berücksichtigen Sie bei der Evaluierung dieser Empfehlungen Ihre Leistungsanforderungen und -kosten sowie Ihre Workloadmuster.

## <a name="quick-check-list"></a>Checkliste

Im folgenden finden eine kurze Checkliste für die optimale Leistung von SQL Server in Azure Virtual Machines:

| Bereich | Optimierungen |
| --- | --- |
| [Größe des virtuellen Computers](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) oder höher für SQL Server Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) oder höher für SQL Server Standard und Web Edition. |
| [Speicher](#storage-guidance) | - Verwenden Sie [SSD Premium](../disks-types.md). Storage Standard empfiehlt sich nur für Entwicklungs- und Testumgebungen.<br/><br/> - Speichern Sie das [Speicherkonto](../../../storage/common/storage-create-storage-account.md) und die SQL Server-VM in derselben Region.<br/><br/> * Deaktivieren Sie auf dem Speicherkonto den [georedundanten Azure-Speicher](../../../storage/common/storage-redundancy.md) (Georeplikation). |
| [Datenträger](#disks-guidance) | - Verwenden Sie mindestens 2 [P30-Datenträger](../disks-types.md#premium-ssd) (1 für Protokolldateien; 1 für Datendateien, einschließlich „TempDB“). Für Workloads, die ca. 50.000 IOPS erfordern, sollten Sie die Verwendung eines SSD Ultra-Datenträgers erwägen. <br/><br/> - Vermeiden Sie die Verwendung von Betriebssystem- oder temporären Datenträgern für die Datenbankspeicherung oder Protokollierung.<br/><br/> - Aktivieren Sie das Caching für Lesevorgänge auf den Datenträgern, auf denen die Datendateien und TempDB-Datendateien gehostet werden.<br/><br/> - Aktivieren Sie kein Caching auf Datenträgern, auf denen die Protokolldatei gehostet wird.  **Wichtig**: Beenden Sie den SQL Server-Dienst, wenn Sie die Cacheeinstellungen für einen Azure-VM-Datenträger ändern.<br/><br/> - Erstellen Sie ein Stripeset mehrerer Azure-Datenträger für Daten, um einen höheren E/A-Durchsatz zu erzielen.<br/><br/> - Formatieren Sie mit dokumentierten Zuordnungsgrößen. <br/><br/> - Legen Sie die TempDB auf der lokalen SSD ab für geschäftskritische SQL Server-Workloads (nach Auswahl der richtigen VM-Größe). |
| [E/A](#io-guidance) |- Aktivieren Sie die Datenbankseitenkomprimierung.<br/><br/> - Aktivieren Sie die sofortige Dateiinitialisierung für Datendateien.<br/><br/> - Begrenzen Sie die automatische Vergrößerung der Datenbank.<br/><br/> - Deaktivieren Sie die automatische Verkleinerung der Datenbank.<br/><br/> - Verschieben Sie alle Datenbanken, einschließlich der Systemdatenbanken, auf Datenträger für Daten.<br/><br/> - Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten.<br/><br/> - Richten Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken ein.<br/><br/> - Aktivieren Sie gesperrte Seiten.<br/><br/> - Wenden Sie SQL Server-Leistungs-Hotfixpakete an. |
| [Featurespezifisch](#feature-specific-guidance) | - Sichern Sie direkt in den Blobspeicher. |

Weitere Informationen dazu, *wie* und *warum* diese Optimierungen vorgenommen werden, finden Sie in den Details und den Leitfäden in den folgenden Abschnitten.

## <a name="vm-size-guidance"></a>Leitfaden für die Größe virtueller Computer

Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung der folgenden [Größen für virtuelle Computer](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 oder höher
* **SQL Server Standard und Web Edition**: DS2_v2 oder höher

VMs der [DSv2-Serie](../sizes-general.md#dsv2-series) unterstützen Storage Premium, was für eine optimale Leistung empfohlen wird. Die hier empfohlenen Größen sind Richtwerte, doch die tatsächliche VM-Größe hängt von den Anforderungen Ihrer Workloads ab. VMs der DSv2-Serie sind universelle VMs, die für eine Vielzahl von Workloads geeignet sind, während andere VM-Größen für bestimmte Workloadtypen optimiert sind. Die [M-Serie](../sizes-memory.md#m-series) bietet beispielsweise die höchste vCPU-Anzahl und Arbeitsspeicher für die größten SQL Server-Workloads. Die [GS-Serie](../sizes-previous-gen.md#gs-series) und [DSv2-Serie 11-15](../sizes-memory.md#dsv2-series-11-15) sind für große Arbeitsspeicheranforderungen optimiert. Beide Serien sind auch mit [eingeschränkten Kerngrößen](../../windows/constrained-vcpu.md) erhältlich, was Kosten bei Workloads mit geringeren Computeanforderungen spart. VMs der [Ls-Serie](../sizes-storage.md) sind für hohen Datenträgerdurchsatz und hohe E/A-Werte optimiert. Es ist wichtig, Ihre spezifische SQL Server-Workload zu prüfen und diese bei der Auswahl der VM-Serie und -Größe zu berücksichtigen.

## <a name="storage-guidance"></a>Leitfaden für Speicher

Virtuelle Computer der DS-Serie (sowie der DSv2- und GS-Serie) unterstützen [SSD Premium](../disks-types.md). Für alle Produktionsworkloads werden Datenträger vom Typ SSD Premium empfohlen.

> [!WARNING]
> Datenträger vom Typ HDD Standard und SSD Standard verfügen über unterschiedliche Latenzen und Bandbreiten und sollten nur für Entwicklungs-/Testworkloads verwendet werden. Für Produktionsworkloads sollte SSD Premium verwendet werden.

Außerdem sollten Sie Ihr Azure-Speicherkonto im selben Rechenzentrum wie Ihre SQL Server-VMs erstellen, um Übertragungsverzögerungen zu verringern. Wenn Sie ein Speicherkonto erstellen, deaktivieren Sie die Georeplikation, da keine konsistente Schreibreihenfolge über mehrere Datenträger gewährleistet werden kann. Konfigurieren Sie stattdessen eine SQL Server-Notfallwiederherstellungstechnologie zwischen zwei Azure-Rechenzentren. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Leitfaden für Datenträger

Es gibt drei Haupttypen von Datenträgern auf einer Azure-VM:

* **Betriebssystemdatenträger**: Wenn Sie einen virtuellen Azure-Computer erstellen, fügt die Plattform dem virtuellen Computer mindestens einen Datenträger (mit der Bezeichnung Laufwerk **C:** ) für das Betriebssystem hinzu. Bei diesem Datenträger handelt es sich um eine VHD, die als Seitenblob gespeichert wird.
* **Temporärer Datenträger**: Virtuelle Azure-Computer enthalten einen weiteren Datenträger, der als temporärer Datenträger bezeichnet wird (Laufwerk **D:** ). Dies ist ein Datenträger für temporären Speicherbereich auf dem Knoten.
* **Datenträger für Daten**: Sie können dem virtuellen Computer auch weitere Datenträger für Daten hinzufügen. Diese werden als Seitenblobs im Speicher gespeichert.

In den folgenden Abschnitten werden Empfehlungen zur Verwendung dieser unterschiedlichen Datenträger erläutert.

### <a name="operating-system-disk"></a>Betriebssystem-Datenträger

Ein Betriebssystem-Datenträger ist eine virtuelle Festplatte, die Sie als aktive Version eines Betriebssystems starten und einbinden können, und die als Laufwerk **C** bezeichnet ist.

Die für den Betriebssystem-Datenträger verwendete Caching-Standardrichtlinie ergibt **Lesen/Schreiben**. Für leistungsabhängige Anwendungen empfiehlt sich die Verwendung von Datenträgern für Daten anstelle des Betriebssystem-Datenträgers. Weitere Informationen finden Sie im Abschnitt über Datenträger für Daten weiter unten.

### <a name="temporary-disk"></a>Temporärer Datenträger

Das temporäre Speicherlaufwerk, das als Laufwerk **D:** bezeichnet wird, wird nicht in Azure Blob Storage beibehalten. Speichern Sie die Datenbank- oder Transaktionsprotokolldateien für Ihre Benutzer nicht auf Laufwerk **D:** .

Das temporäre Laufwerk auf virtuellen Computern der D-Serie, Dv2-Serie und G-Serie ist SSD-basiert. Falls Ihre Workload intensiv TempDB nutzt (beispielsweise für temporäre Objekte oder komplexe Verknüpfungen), kann das Speichern von TempDB auf Laufwerk **D:** zu einem höheren Durchsatz und einer geringeren TempDB-Wartezeit führen. Ein Beispielszenario finden Sie in der TempDB-Diskussion im folgenden Blogbeitrag: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm) (Richtlinien zur Speicherkonfiguration für SQL Server auf einer Azure-VM).

Für virtuelle Computer, die SSD Premium unterstützen (DS-Serie, DSv2-Serie und GS-Serie), sollten Sie TempDB auf einem Datenträger speichern, der SSD Premium mit aktiviertem Lesecache unterstützt.

Es gibt eine Ausnahme von dieser Empfehlung: _Wenn die „tempdb“-Auslastung schreibintensiv ist, können Sie eine höhere Leistung erzielen, indem Sie „tempdb“ auf dem lokalen Laufwerk **D:** speichern, das für diese Computergrößen auch SSD-basiert ist._

### <a name="data-disks"></a>Datenträger

* **Verwenden von Datenträgern für Daten- und Protokolldateien**: Wenn Sie kein Datenträgerstriping verwenden, sollten Sie zwei P30-Datenträger vom Typ SSD Premium verwenden, wobei sich auf einem Datenträger die Protokolldateien und auf dem anderen die Daten- und TempDB-Dateien befinden. Jeder Datenträger vom Typ SSD Premium stellt je nach Größe IOPs und Bandbreite (MB/s) bereit. Dies ist im Artikel [Auswählen eines Datenträgertyps](../disks-types.md) beschrieben. Wenn Sie ein Verfahren zum Datenträgerstriping verwenden, z. B. Speicherplätze, erzielen Sie eine optimale Leistung, indem Sie zwei Pools einrichten, einen für die Protokolldatei(en) und den anderen für die Datendateien. Wenn Sie jedoch planen, SQL Server-Failoverclusterinstanzen (FCI) zu verwenden, müssen Sie einen Pool konfigurieren.

   > [!TIP]
   > - Testergebnisse zu verschiedenen Datenträger- und Workloadkonfigurationen finden Sie im folgenden Blogbeitrag: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Richtlinien zur Speicherkonfiguration für SQL Server auf einer Azure-VM).
   > - Erwägen Sie für unternehmenskritische Leistung für SQL Server-Instanzen, die ca. 50.000 IOPS benötigen, 10 -P30-Datenträger durch einen SSD Ultra-Datenträger zu ersetzen. Weitere Informationen finden Sie im folgenden Blogbeitrag: [Unternehmenskritische Leistung mit SSD Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Beim Bereitstellen einer SQL Server-VM im Portal haben Sie die Möglichkeit, Ihre Speicherkonfiguration zu bearbeiten. Je nach Konfiguration konfiguriert Azure einen oder mehrere Datenträger. Mehrere Datenträger werden in einem einzelnen Speicherpool mit Striping zusammengefasst. Daten- und Protokolldateien befinden sich in dieser Konfiguration zusammen. Weitere Informationen finden Sie unter [Speicherkonfiguration für SQL Server-VMs](virtual-machines-windows-sql-server-storage-configuration.md).

* **Datenträgerstriping**: Für einen höheren Durchsatz können Sie zusätzliche Datenträger für Daten hinzufügen und Datenträgerstriping verwenden. Um die Anzahl der Datenträger zu ermitteln, müssen Sie die Anzahl der IOPS und die Bandbreite analysieren, die für die Protokolldateien und für Ihre Daten und die TempDB-Dateien erforderlich sind. Beachten Sie, dass verschiedene VM-Größen über unterschiedliche Grenzwerte für die unterstützte Anzahl der IOPS und die Bandbreiten verfügen. Informationen finden Sie in den Tabellen zu IOPS pro [Größe des virtuellen Computers](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Verwenden Sie die folgenden Richtlinien:

  * Verwenden Sie [Speicherplätze](https://technet.microsoft.com/library/hh831739.aspx) für Windows 8 und Windows Server 2012 oder höher mit den folgenden Richtlinien:

      1. Legen Sie die Überlappung (Stripsetgröße) für OLTP-Workloads auf 64 KB (65536 Bytes) und für Data Warehousing-Workloads auf 256 KB (262144 Bytes) fest, um Leistungseinbußen durch falsche Partitionsausrichtung zu vermeiden. Dies muss mit PowerShell festgelegt werden.
      2. Festgelegte Anzahl der Spalten = Anzahl der physischen Datenträger. Verwenden Sie PowerShell, wenn Sie mehr als 8 Datenträger (keine Server-Manager-Benutzeroberfläche) konfigurieren. 

    PowerShell erstellt z.B. wie folgt einen neuen Speicherpool mit der Überlappungsgröße auf 64 KB und die Anzahl der Spalten auf 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Für Windows 2008 R2 oder früher können Sie dynamische Datenträger (Betriebssystem-Stripesetvolumes) verwenden, und die Stripesetgröße ist immer 64 KB. Beachten Sie, dass diese Option seit Windows 8 / Windows Server 2012 veraltet ist. Informationen hierzu finden Sie in der Supporterklärung unter [Virtual Disk Service is transitioning to Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)(Übergang des Diensts für virtuelle Datenträger in die Windows-Speicherverwaltungs-API, in englischer Sprache).

  * Bei Verwendung von [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) (Storage Spaces Direct, S2D) mit [SQL Server-Failoverclusterinstanzen](virtual-machines-windows-portal-sql-create-failover-cluster.md) müssen Sie einen einzelnen Pool konfigurieren. Auch wenn in einem einzelnen Pool unterschiedliche Volumes erstellt werden können, sollten Sie darauf achten, dass alle die gleichen Merkmale (z. B. die gleiche Cacherichtlinie) aufweisen.

  * Bestimmen Sie auf Basis der erwarteten Auslastung die Anzahl der jedem Speicherpool zugeordneten Datenträger. Bedenken Sie, dass verschiedene VM-Größen unterschiedlich viele angefügte Datenträger für Daten unterstützen. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Wenn Sie keine Datenträger vom Typ SSD Premium nutzen (Entwicklungs-/Testszenarien), sollten Sie die für Ihre [Größe des virtuellen Computers](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maximal unterstützte Anzahl von Datenträgern für Daten hinzufügen und Datenträgerstriping verwenden.

* **Cacherichtlinie**: Berücksichtigen Sie die folgenden Empfehlungen für die Cacherichtlinie (abhängig von Ihrer Speicherkonfiguration).

  * Wenn Sie separate Datenträger für Daten- und Protokolldateien verwenden, aktivieren Sie das Zwischenspeichern für Lesevorgänge für die Datenträger, auf denen Ihre Datendateien und TempDB-Datendateien gehostet werden. Dadurch lässt sich die Leistung mitunter erheblich verbessern. Aktivieren Sie die Zwischenspeicherung nicht für den Datenträger mit der Protokolldatei, da sich dadurch die Leistung minimal verschlechtert.

  * Wenn Sie Datenträgerstriping in einem einzelnen Speicherpool verwenden, profitieren die meisten Workloads vom Zwischenspeichern für Lesevorgänge. Wenn Sie gesonderte Speicherpools für die Protokoll- und Datendateien haben, aktivieren Sie die Zwischenspeicherung für Lesevorgänge nur für den Speicherpool der Datendateien. Bei bestimmten schreiblastigen Workloads lässt sich unter Umständen ohne Zwischenspeicherung eine höhere Leistung erzielen. Dies kann nur im Rahmen von Tests ermittelt werden.

  * Die obigen Empfehlungen gelten für SSD Premium-Datenträger. Wenn Sie keine SSD Premium-Datenträger verwenden, sollten Sie das Caching für reguläre Datenträger nicht aktivieren.

  * Informationen zum Konfigurieren des Datenträgercachings finden Sie in den folgenden Artikeln. Für das klassische Bereitstellungsmodell (ASM): [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) und [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Für das Azure Resource Manager-Bereitstellungsmodell: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) und [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Beenden Sie den SQL Server-Dienst beim Ändern der Cacheeinstellung der Azure-VM-Datenträger, um eine Beschädigung der Datenbank zu vermeiden.

* **Größe der NTFS-Zuordnungseinheiten**: Beim Formatieren des Datenträgers für Daten empfiehlt es sich, als Größe der Zuordnungseinheiten für Daten- und Protokolldateien sowie für TempDB einen Wert von 64 KB zu verwenden.

* **Bewährte Methoden für die Datenträgerverwaltung**: Beenden Sie den SQL Server-Dienst, wenn Sie einen Datenträger für Daten entfernen oder dessen Cachetyp ändern. Wenn die Cacheeinstellungen auf dem Betriebssystemdatenträger geändert werden, beendet Azure den virtuellen Computer, ändert den Cachetyp und startet den virtuellen Computer neu. Wenn die Cacheeinstellungen eines Datenträgers für Daten geändert werden, wird der virtuelle Computer nicht beendet, aber der Datenträger für Daten wird während der Änderung vom virtuellen Computer getrennt und dann erneut angefügt.

  > [!WARNING]
  > Beenden Sie den SQL Server-Dienst während dieser Vorgänge nicht, kann eine Beschädigung der Datenbank die Folge sein.


## <a name="io-guidance"></a>E/A-Leitfaden

* Die besten Ergebnisse mit SSD Premium-Datenträgern werden erzielt, wenn Sie Ihre Anwendung und Anforderungen parallelisieren. SSD Premium ist für Szenarien bestimmt, in denen die E/A-Warteschlangentiefe größer als 1 ist, sodass nur geringe oder gar keine Leistungssteigerungen für serielle Singlethread-Anforderungen erreicht werden (auch wenn sie speicherintensiv sind). Dies kann sich z. B. auf die Singlethread-Testergebnisse der Leistungsanalysetools, z. B. SQLIO, auswirken.

* Ziehen Sie die Verwendung der [Datenbankseitenkomprimierung](https://msdn.microsoft.com/library/cc280449.aspx) in Betracht, weil Sie dadurch die Leistung von E/A-intensiven Workloads verbessern können. Die Datenkomprimierung erhöht jedoch möglicherweise den CPU-Verbrauch auf dem Datenbankserver.

* Erwägen Sie die Aktivierung der sofortigen Dateiinitialisierung, um die für die anfängliche Dateizuordnung erforderliche Zeit zu verringern. Um die sofortige Dateiinitialisierung nutzen zu können, gewähren Sie dem SQL Server-Dienstkonto (MSSQLSERVER) die Berechtigung SE_MANAGE_VOLUME_NAME, und fügen Sie es zur Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzu. Wenn Sie ein SQL Server-Plattformimage für Azure verwenden, wird das Standarddienstkonto (NT Service\MSSQLSERVER) nicht der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt. Anders ausgedrückt: Die sofortige Dateiinitialisierung wird in einem SQL Server Azure-Plattformimages nicht aktiviert. Starten Sie den SQL Server-Dienst neu, nachdem Sie das SQL Server-Dienstkonto der Sicherheitsrichtlinie **Durchführen von Volumewartungsaufgaben** hinzugefügt haben. Möglicherweise sind bei Verwendung dieses Features bestimmte Sicherheitsaspekte zu berücksichtigen. Weitere Informationen finden Sie unter [Datenbankdatei-Initialisierung](https://msdn.microsoft.com/library/ms175935.aspx).

* **automatische Vergrößerung** gilt lediglich als Behelfslösung für unerwartetes Wachstum. Führen Sie die tägliche Verwaltung Ihres Daten- und Protokolldateiwachstums nicht durch automatische Vergrößerung. Wenn die automatische Vergrößerung verwendet wird, vergrößern Sie die Datei vorab mit dem Switch "Größe".

* Stellen Sie sicher, dass **Automatische Verkleinerung** deaktiviert ist, um unnötigen Mehraufwand zu vermeiden, der sich negativ auf die Leistung auswirken kann.

* Verschieben Sie alle Datenbanken, einschließlich der Systemdatenbanken, auf Datenträger für Daten. Weitere Informationen finden Sie unter [Verschieben von Systemdatenbanken](https://msdn.microsoft.com/library/ms345408.aspx).

* Verschieben Sie die Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Datenträger für Daten. Dies können Sie mit dem SQL Server-Konfigurations-Manager vornehmen, indem Sie mit der rechten Maustaste auf die SQL Server-Instanz klicken und „Eigenschaften“ auswählen. Die Einstellungen für Fehlerprotokolle und Ablaufverfolgungsdateien können auf der Registerkarte **Startparameter** geändert werden. Das Sicherungsverzeichnis wird auf der Registerkarte **Erweitert** angegeben. Der folgende Screenshot zeigt, wo sich der Startparameter für Fehlerprotokolle befindet.

    ![Screenshot eines SQL-Fehlerprotokolls](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Richten Sie standardmäßige Dateispeicherorte für Sicherungen und Datenbanken ein. Verwenden Sie die Empfehlungen in diesem Artikel, und nehmen Sie die Änderungen im Fenster mit den Servereigenschaften vor. Eine Anleitung finden Sie unter [Anzeigen oder Ändern der Standardspeicherorte für Daten- und Protokolldateien (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Der folgende Screenshot zeigt, wo diese Änderungen vorgenommen werden.

    ![Datenprotokoll- und Sicherungsdateien für SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Aktivieren Sie gesperrte Seiten, um E/A- und Auslagerungsaktivitäten zu verringern. Weitere Informationen finden Sie unter [Aktivieren der Option zum Sperren von Seiten im Speicher (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Wenn Sie SQL Server 2012 ausführen, installieren Sie Service Pack 1, kumulatives Update 10. Dieses Update enthält ein Fix für schlechte E/A-Leistung beim Ausführen von SELECT in temporären Tabellenanweisungen in SQL Server 2012 Informationen hierzu finden Sie in diesem [Knowledge Base-Artikel](https://support.microsoft.com/kb/2958012).

* Es könnte auch sinnvoll sein, alle Datendateien beim Übertragen in bzw. aus Azure zu komprimieren.

## <a name="feature-specific-guidance"></a>Featurespezifische Anleitungen

Für manche Bereitstellungen können durch Verwendung erweiterter Konfigurationsmethoden zusätzliche Leistungsvorteile erzielt werden. In der folgenden Liste sind einige SQL Server-Features zusammengestellt, mit denen Sie eine bessere Leistung erzielen können:

### <a name="backup-to-azure-storage"></a>Sicherung in Azure Storage
Wenn Sie Sicherungen für eine SQL Server-Instanz durchführen, die auf virtuellen Azure-Computern ausgeführt wird, können Sie die [SQL Server-Sicherung über URLs](https://msdn.microsoft.com/library/dn435916.aspx) verwenden. Dieses Feature steht ab SQL Server 2012 SP1 CU2 zur Verfügung und wird für Sicherungen auf die angefügten Datenträgern für Daten empfohlen. Bei Sicherungen/Wiederherstellungen nach/von Azure Storage folgen Sie den Empfehlungen unter [SQL Server-URL-Sicherung – bewährte Methoden und Problembehandlung](https://msdn.microsoft.com/library/jj919149.aspx)und „Wiederherstellen von in Microsoft Azure gespeicherten Sicherungen“. Darüber hinaus können Sie diese Sicherungen mit [automatisierten Sicherungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-automated-backup.md)automatisieren.

In Versionen vor SQL Server 2012 können Sie das [Microsoft SQL Server Backup to Microsoft Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)verwenden. Dieses Tool hilft, den Sicherungsdurchsatz durch Verwenden mehrerer Sicherungsstripeset-Ziele zu erhöhen.

### <a name="sql-server-data-files-in-azure"></a>SQL Server-Datendateien in Azure

Das neue Feature [SQL Server-Datendateien in Azure](https://msdn.microsoft.com/library/dn385720.aspx) ist ab SQL Server 2014 verfügbar. Das Ausführen von SQL Server mit Datendateien in Azure zeigt vergleichbare Leistungseigenschaften wie die Verwendung von Azure-Datenträgern.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Failoverclusterinstanz und Speicherplätze

Wenn Sie Speicherplätze verwenden, deaktivieren Sie beim Hinzufügen von Knoten zum Cluster auf der Seite **Bestätigung** das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden**. 

![Deaktivieren von geeignetem Speicher](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Wenn Sie Speicherplätze verwenden und das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden.** nicht deaktivieren, trennt Windows die virtuellen Datenträger während des Clusterprozesses. Sie werden daher erst im Datenträger-Manager oder -Explorer angezeigt, nachdem die Speicherplätze aus dem Cluster entfernt und mithilfe von PowerShell erneut angefügt wurden. Mit Speicherplätzen werden mehrere Datenträger in Speicherpools gruppiert. Weitere Informationen finden Sie unter [Speicherplätze](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Speicher und Leistung finden Sie unter [Richtlinien für die Speicherkonfiguration für SQL Server auf virtuellen Azure-Computern](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).

Best Practices für die Sicherheit finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-security.md).

Weitere Artikel zu virtuellen SQL Server-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md). Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](virtual-machines-windows-sql-server-iaas-faq.md) weitere Informationen.
