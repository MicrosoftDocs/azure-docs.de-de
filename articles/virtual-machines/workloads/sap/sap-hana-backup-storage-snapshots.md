---
title: SAP HANA-Azure-Sicherung auf der Grundlage von Speichermomentaufnahmen | Microsoft-Dokumentation
description: Für SAP HANA auf virtuellen Azure-Computern stehen grundsätzlich zwei Sicherungsoptionen zur Verfügung. In diesem Artikel wird die SAP HANA-Sicherung basierend auf Speichermomentaufnahmen behandelt.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 74f47344afff630a8633b340ea4ce21db28db7ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60936553"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>SAP HANA-Sicherung auf der Grundlage von Speichermomentaufnahmen

## <a name="introduction"></a>Einführung

Dieser Artikel ist Teil einer dreiteiligen Reihe zur SAP HANA-Sicherung. [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) enthält eine Übersicht und Informationen zu den ersten Schritten, und unter [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md) wird die dateibasierte Sicherung behandelt.

Bei Verwendung einer VM-Sicherungsfunktion für ein All-in-One-Einzelinstanz-Demosystem ist es ratsam, eine VM-Sicherung durchzuführen, anstatt HANA-Sicherungen auf Betriebssystemebene zu verwalten. Eine Alternative hierzu ist die Erstellung von Azure-Blob-Momentaufnahmen zum Erstellen von Kopien einzelner virtueller Datenträger, die an einen virtuellen Computer angefügt sind, und die Beibehaltung der HANA-Datendateien. Ein kritischer Punkt ist die App-Konsistenz beim Erstellen einer VM-Sicherung oder Datenträger-Momentaufnahme, während das System aktiv ist. Informationen hierzu finden Sie unter _Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen_ im Artikel [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) dieser Reihe. SAP HANA verfügt über ein Feature, das diese Arten von Speichermomentaufnahmen unterstützt.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA-Momentaufnahmen als zentraler Bestandteil anwendungskonsistenter Sicherungen

In SAP HANA gibt es eine Funktion, die das Erstellen von Speichermomentaufnahmen unterstützt. Dies ist auf Systeme mit einem Container beschränkt. In Szenarien mit SAP HANA-MCS mit mehr als einem Mandanten wird diese Art von SAP HANA-Datenbankmomentaufnahme nicht unterstützt (siehe [Erstellen einer Speichermomentaufnahme (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Dies funktioniert wie folgt:

- Vorbereiten auf eine Speichermomentaufnahme durch die Initiierung der SAP HANA-Momentaufnahme
- Ausführen der Speichermomentaufnahme (z.B. Azure-Blob-Momentaufnahme)
- Bestätigen der SAP HANA-Momentaufnahme

![Screenshot: Erstellung einer SAP HANA-Datenmomentaufnahme per SQL-Anweisung](media/sap-hana-backup-storage-snapshots/image011.png)

In diesem Screenshot ist dargestellt, dass eine SAP HANA-Datenmomentaufnahme per SQL-Anweisung erstellt werden kann.

![Die Momentaufnahme wird in SAP HANA Studio auch im Sicherungskatalog angezeigt](media/sap-hana-backup-storage-snapshots/image012.png)

Die Momentaufnahme wird in SAP HANA Studio auch im Sicherungskatalog angezeigt.

![Auf dem Datenträger wird die Momentaufnahme im SAP HANA-Datenverzeichnis angezeigt](media/sap-hana-backup-storage-snapshots/image013.png)

Auf dem Datenträger wird die Momentaufnahme im SAP HANA-Datenverzeichnis angezeigt.

Es muss auch sichergestellt werden, dass die Konsistenz des Dateisystems gewährleistet ist, bevor die Speichermomentaufnahme ausgeführt wird, während sich SAP HANA im Vorbereitungsmodus für Momentaufnahmen befindet. Informationen hierzu finden Sie unter _Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen_ im Artikel [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) dieser Reihe.

Nachdem die Speichermomentaufnahme erstellt wurde, ist es wichtig, dass die SAP HANA-Momentaufnahme bestätigt wird. Hierzu muss eine entsprechende SQL-Anweisung ausgeführt werden: BACKUP DATA CLOSE SNAPSHOT (siehe [BACKUP DATA CLOSE SNAPSHOT-Anweisung (Sicherung und Wiederherstellung)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Bestätigen Sie die HANA-Momentaufnahme. Aufgrund des &quot;Copy-on-Write&quot;-Vorgangs benötigt SAP HANA im Vorbereitungsmodus für Momentaufnahmen ggf. zusätzlichen Datenträger-Speicherplatz. Es ist erst möglich, neue Sicherungen zu starten, nachdem die SAP HANA-Momentaufnahme bestätigt wurde.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA-VM-Sicherung per Azure Backup-Dienst

Der Backup-Agent des Azure Backup-Diensts ist für Linux-VMs nicht verfügbar. Darüber hinaus besitzt Linux keine ähnliche Funktionalität wie es bei Windows mit VSS der Fall ist.  Zur Verwendung der Azure-Sicherung auf Datei- bzw. Verzeichnisebene werden SAP HANA-Sicherungsdateien auf eine Windows-VM kopiert, und anschließend wird der Backup-Agent verwendet. 

Andernfalls ist mit dem Azure Backup-Dienst nur eine vollständige Linux-VM-Sicherung möglich. Weitere Informationen finden Sie unter [Übersicht über die Funktionen in Azure Backup](../../../backup/backup-introduction-to-azure-backup.md).

Der Azure Backup-Dienst verfügt über eine Option zum Sichern und Wiederherstellen einer VM. Weitere Informationen zu diesem Dienst und zur Funktionsweise finden Sie im Artikel [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](../../../backup/backup-azure-vms-introduction.md).

In diesem Artikel wird auf zwei wichtige Aspekte hingewiesen, die berücksichtigt werden sollten:

_&quot;Für virtuelle Linux-Computer sind nur dateikonsistente Sicherungen möglich, da Linux keine dem VSS entsprechende Plattform umfasst.&quot;_

_&quot;Anwendungen müssen einen eigenen &quot;Reparaturmechanismus&quot; für die wiederhergestellten Daten implementieren.&quot;_

Aus diesem Grund muss sichergestellt werden, dass sich SAP HANA beim Starten der Sicherung in einem konsistenten Zustand befindet. Informationen hierzu finden Sie unter _SAP HANA-Momentaufnahmen_ weiter oben im Dokument. Unter Umständen kann aber ein Problem auftreten, wenn SAP HANA in diesem Vorbereitungsmodus für Momentaufnahmen verbleibt. Weitere Informationen finden Sie unter [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) (Erstellen einer Speichermomentaufnahme (SAP HANA Studio)).

Im Artikel wird auf Folgendes hingewiesen:

_&quot;Es wird dringend empfohlen, eine Speichermomentaufnahme nach ihrer Erstellung so schnell wie möglich zu bestätigen oder zu verwerfen. Während die Speichermomentaufnahme vorbereitet oder erstellt wird, werden die für die Momentaufnahme relevanten Daten eingefroren. Während des Einfrierzustands der für die Momentaufnahme relevanten Daten können in der Datenbank trotzdem Änderungen vorgenommen werden. Diese Änderungen führen nicht dazu, dass sich die eingefrorenen Daten für die Momentaufnahme ändern. Stattdessen werden die Änderungen im Datenbereich an Speicherorte geschrieben, die vom Bereich für die Speichermomentaufnahme getrennt sind. Außerdem werden die Änderungen in ein Protokoll geschrieben. Je länger die für die Momentaufnahme relevanten Daten aber eingefroren bleiben, desto stärker kann die Größe des Datenvolume zunehmen.&quot;_

Azure Backup stellt die Konsistenz des Dateisystems mithilfe von Azure-VM-Erweiterungen sicher. Diese Erweiterungen sind nicht als eigenständige Komponenten verfügbar und funktionieren nur in Verbindung mit dem Azure Backup-Dienst. Trotzdem ist es erforderlich, Skripts zum Erstellen und Löschen einer SAP HANA-Momentaufnahme bereitzustellen, um die App-Konsistenz zu gewährleisten.

Azure Backup umfasst vier Hauptphasen:

- Ausführen eines Vorbereitungsskripts – das Skript muss eine SAP HANA-Momentaufnahme erstellen
- Erstellen der Momentaufnahme
- Ausführen eines Skripts nach der Momentaufnahme – das Skript muss die SAP HANA-Momentaufnahme löschen, die vom Vorbereitungsskript erstellt wurde
- Übertragen von Daten in einen Tresor

Informationen dazu, wo Sie diese Skripts kopieren können, sowie Informationen zur genauen Funktionsweise von Azure Backup finden Sie in den folgenden Artikeln:

- [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Anwendungskonsistente Sicherung von virtuellen Linux-Computern in Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Zum gegenwärtigen Zeitpunkt hat Microsoft keine Vorbereitungsskripts und Skripts nach der Momentaufnahme für SAP HANA veröffentlicht. Sie als Kunde oder Systemintegrator müssen diese Skripts erstellen und die Prozedur auf Grundlage der oben genannten Dokumentation konfigurieren.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Wiederherstellen aus einer anwendungskonsistenten Sicherung für einen virtuellen Computer
Der Wiederherstellungsvorgang für eine anwendungskonsistente Sicherung, die mit Azure Backup erstellt wurde, ist in dem Artikel [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) dokumentiert. 

> [!IMPORTANT]
> Der Artikel [Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) enthält eine Liste von Ausnahmen sowie Schritte bei Verwendung von Stripesets für Datenträger. Stripesetdatenträger sind wahrscheinlich die übliche Konfiguration des virtuellen Computers für SAP HANA. Daher ist es wichtig, den Artikel zu lesen und den Wiederherstellungsvorgang für die im Artikel aufgeführten Fälle zu testen. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA-Lizenzschlüssel und VM-Wiederherstellung per Azure Backup-Dienst

Der Azure Backup-Dienst ist dafür ausgelegt, während der Wiederherstellung eine neue VM zu erstellen. Derzeit ist nicht geplant, eine &quot;direkte&quot; Wiederherstellung einer vorhandenen Azure-VM durchzuführen.

![Abbildung: Wiederherstellungsoption des Azure-Diensts im Azure-Portal](media/sap-hana-backup-storage-snapshots/image019.png)

In dieser Abbildung ist die Wiederherstellungsoption des Azure-Diensts im Azure-Portal dargestellt. Sie können zwischen der Erstellung einer VM während der Wiederherstellung und der Wiederherstellung der Datenträger wählen. Nach dem Wiederherstellen der Datenträger ist es zusätzlich erforderlich, eine neue VM zu erstellen. Wenn in Azure eine neue VM erstellt wird, ändert sich die eindeutige VM-ID (siehe [Accessing and Using Azure VM Unique ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) (Zugreifen auf und Verwenden einer eindeutigen Azure-VM-ID)).

![Abbildung: Eindeutige Azure-VM-ID vor und nach der Wiederherstellung mit dem Azure Backup-Dienst](media/sap-hana-backup-storage-snapshots/image020.png)

In dieser Abbildung ist die eindeutige Azure-VM-ID vor und nach der Wiederherstellung mit dem Azure Backup-Dienst dargestellt. Diese eindeutige VM-ID wird für den SAP-Hardwareschlüssel verwendet, der für die SAP-Lizenzierung genutzt wird. Aus diesem Grund muss nach einer VM-Wiederherstellung eine neue SAP-Lizenz installiert werden.

Während der Erstellung dieser Sicherungsanleitung wurde die Vorschauversion eines neuen Azure Backup-Features vorgestellt. Mit diesem Feature ist basierend auf der VM-Momentaufnahme, die für die VM-Sicherung erstellt wurde, eine Wiederherstellung auf Dateiebene möglich. So entfällt die Anforderung, eine neue VM bereitstellen zu müssen. Die eindeutige VM-ID bleibt unverändert, und es ist kein neuer SAP HANA-Lizenzschlüssel erforderlich. Weitere Informationen und eine Dokumentation zu diesem Feature werden bereitgestellt, nachdem es vollständig getestet wurde.

Für Azure Backup ist dann zu einem späteren Zeitpunkt die Sicherung von einzelnen virtuellen Azure-Datenträgern möglich, einschließlich der Dateien und Verzeichnisse der VM. Ein großer Vorteil von Azure Backup ist die Verwaltung aller eigenen Sicherungen, sodass dies nicht vom Kunden durchgeführt werden muss. Wenn eine Wiederherstellung erforderlich ist, wählt Azure Backup die richtige Sicherung aus.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA-VM-Sicherung per manueller Datenträger-Momentaufnahme

Anstatt den Azure Backup-Dienst zu verwenden, können Sie auch eine individuelle Sicherungslösung konfigurieren, indem Sie Blob-Momentaufnahmen von Azure-VHDs manuell mit PowerShell erstellen. Eine Beschreibung dieser Schritte finden Sie unter [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Verwenden von Blob-Momentaufnahmen mit PowerShell).

Dies ermöglicht zwar eine höhere Flexibilität, aber die weiter oben in diesem Dokument erwähnten Probleme werden hierdurch nicht gelöst:

- Sie müssen weiterhin sicherstellen, dass sich SAP HANA in einem konsistenten Zustand befindet, indem Sie eine SAP HANA-Momentaufnahme erstellen.
- Der Betriebssystemdatenträger kann auch dann nicht überschrieben werden, wenn die Zuordnung der VM aufgehoben wird, da in einem Fehler angegeben wird, dass ein Lease vorhanden ist. Dies funktioniert nur nach dem Löschen der VM, wonach dann aber wieder eine neue eindeutige VM-ID benötigt wird und eine neue SAP-Lizenz installiert werden muss.

![Für eine Azure-VM können auch nur die Datenträger für Daten wiederhergestellt werden](media/sap-hana-backup-storage-snapshots/image021.png)

Für eine Azure-VM können auch nur die Datenträger für Daten wiederhergestellt werden. So wird vermieden, dass eine neue eindeutige VM-ID erforderlich ist und die SAP-Lizenz ungültig wird:

- Für den Test wurden zwei Azure-Datenträger an eine VM angefügt, und zusätzlich wurde eine Software-RAID-Komponente definiert. 
- Mit dem SAP HANA-Feature für Momentaufnahmen wurde bestätigt, dass sich SAP HANA in einem konsistenten Zustand befindet.
- Das Dateisystem wurde eingefroren. (Informationen hierzu finden Sie unter _Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen_ im Artikel [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) dieser Reihe.)
- Für beide Datenträger wurden Blob-Momentaufnahmen erstellt.
- Der Einfrierzustand des Dateisystems wurde aufgehoben.
- Die SAP HANA-Momentaufnahme wurde bestätigt.
- Zum Wiederherstellen der Datenträger wurde die VM heruntergefahren, und beide Datenträger wurden getrennt.
- Nach dem Trennen der Datenträger wurden diese durch die vorherigen Blob-Momentaufnahmen überschrieben.
- Anschließend wurden die wiederhergestellten virtuellen Datenträger wieder an die VM angefügt.
- Nach dem Starten der VM funktionierte die Software-RAID-Komponente fehlerfrei, und die Zurücksetzung auf die Zeit der Blob-Momentaufnahme wurde durchgeführt.
- HANA wurde auf die HANA-Momentaufnahme zurückgesetzt.

Wenn es möglich wäre, SAP HANA vor der Erstellung der Blob-Momentaufnahmen herunterzufahren, wäre das Verfahren weniger komplex. In diesem Fall könnte die HANA-Momentaufnahme übersprungen werden, und falls im System keine anderen Vorgänge ablaufen, könnte auch das Einfrieren des Dateisystems entfallen. Die Komplexität erhöht sich, wenn Momentaufnahmen bei vollem Onlinebetrieb erstellt werden müssen. Informationen hierzu finden Sie unter _Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen_ im Artikel [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) dieser Reihe.

## <a name="next-steps"></a>Nächste Schritte
* [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Sicherungshandbuch für SAP HANA in Azure Virtual Machines) enthält eine Übersicht und Informationen zu den ersten Schritten.
* Unter [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md) wird die dateibasierte Sicherung behandelt.
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
