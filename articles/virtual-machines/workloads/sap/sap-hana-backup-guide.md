---
title: Sicherungsanleitung für SAP HANA in Azure Virtual Machines | Microsoft-Dokumentation
description: In der Sicherungsanleitung für SAP HANA werden die beiden wichtigsten Sicherungsmöglichkeiten für SAP HANA auf virtuellen Azure-Computern beschrieben.
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
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60937026"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Sicherungsanleitung für SAP HANA in Azure Virtual Machines

## <a name="getting-started"></a>Erste Schritte

In der Sicherungsanleitung für SAP HANA auf virtuellen Azure-Computern (Azure Virtual Machines) werden nur Azure-spezifische Themen beschrieben. Informationen zu allgemeinen Aspekten der SAP HANA-Sicherung finden Sie in der Dokumentation zu SAP HANA (siehe _Dokumentation zur SAP HANA-Sicherung_ weiter unten in diesem Artikel).

Der Schwerpunkt dieses Artikels liegt auf den zwei wichtigsten Sicherungsmöglichkeiten für SAP HANA auf virtuellen Azure-Computern:

- HANA-Sicherung im Dateisystem eines virtuellen Azure-Computers mit Linux (siehe [SAP HANA-Azure-Sicherung auf Dateiebene](sap-hana-backup-file-level.md))
- HANA-Sicherung basierend auf Speichermomentaufnahmen per manueller Nutzung der Azure Storage Blob-Momentaufnahmenfunktion oder des Azure Backup-Diensts (siehe [SAP HANA backup based on storage snapshots](sap-hana-backup-storage-snapshots.md) (SAP HANA-Sicherung auf der Grundlage von Speichermomentaufnahmen))

SAP HANA verfügt über eine Sicherungs-API, sodass Sicherungstools von Drittanbietern direkt in SAP HANA integriert werden können. (Dies wird im Rahmen dieser Anleitung nicht beschrieben.) Basierend auf dieser API ist derzeit keine direkte Integration von SAP HANA in den Azure Backup-Dienst möglich.

SAP HANA wird offiziell von verschiedenen Azure-VM-Typen, z.B der Azure M-Serie, unterstützt. Eine vollständige Liste der für SAP HANA zertifizierten Azure-VMs finden Sie unter [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Zertifizierte IaaS-Plattformen). Dieser Artikel wird aktualisiert, wenn neue Angebote für SAP HANA in Azure verfügbar sind.

Für Azure ist auch eine SAP HANA-Hybridlösung verfügbar, bei der SAP HANA nicht virtualisiert auf physischen Servern ausgeführt wird. In dieser Azure-Sicherungsanleitung für SAP HANA wird aber eine reine Azure-Umgebung beschrieben, in der SAP HANA auf einer Azure-VM ausgeführt wird (nicht die Ausführung von SAP HANA auf &quot;großen Instanzen&quot;). Weitere Informationen zu dieser Sicherungslösung auf &quot;großen Instanzen&quot; basierend auf Speichermomentaufnahmen finden Sie unter [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](hana-overview-architecture.md).

Allgemeine Informationen zu SAP-Produkten, die in Azure unterstützt werden, finden Sie im [SAP-Hinweis 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Die folgenden drei Abbildungen enthalten eine Übersicht über die SAP HANA-Sicherungsoptionen unter Verwendung der derzeitigen nativen Azure-Funktionen sowie drei potenzielle zukünftige Sicherungsszenarien. In den verwandten Artikeln [SAP HANA Azure Backup on file level](sap-hana-backup-file-level.md) (SAP HANA-Sicherung mit Azure auf Dateiebene) und [SAP HANA backup based on storage snapshots](sap-hana-backup-storage-snapshots.md) (SAP HANA-Sicherung basierend auf Speichermomentaufnahmen) werden diese Optionen ausführlicher beschrieben, z.B. Größen- und Leistungsaspekte für SAP HANA-Sicherungen mit mehreren Terabyte.

![Abbildung mit Darstellung von zwei Möglichkeiten zum Speichern des aktuellen VM-Status](media/sap-hana-backup-guide/image001.png)

In dieser Abbildung ist die Möglichkeit zum Speichern des aktuellen VM-Status dargestellt – entweder per Azure Backup-Dienst oder manueller Momentaufnahme von VM-Datenträgern. Bei diesem Ansatz ist keine Verwaltung von SAP HANA-Sicherungen erforderlich. Die Herausforderung besteht beim Szenario mit Datenträger-Momentaufnahmen darin, die Konsistenz des Dateisystems und einen anwendungskonsistenten Datenträgerzustand zu erreichen. Auf die Konsistenz wird weiter unten in diesem Artikel im Abschnitt _SAP HANA-Datenkonsistenz beim Aufnehmen von Speichermomentaufnahmen_ eingegangen. Funktionen und Einschränkungen des Azure Backup-Diensts in Bezug auf SAP HANA-Sicherungen werden ebenfalls später in diesem Artikel beschrieben.

![Abbildung mit Optionen zur Erstellung einer SAP HANA-Dateisicherung auf der VM](media/sap-hana-backup-guide/image002.png)

In dieser Abbildung sind Optionen zum Erstellen einer SAP HANA-Dateisicherung auf der VM und zum anschließenden Speichern der HANA-Sicherungsdateien an einem anderen Speicherort mit unterschiedlichen Tools dargestellt. Zum Erstellen einer HANA-Sicherung ist mehr Zeit als bei einer Sicherungslösung mit Momentaufnahmen erforderlich, aber hinsichtlich der Integrität und Konsistenz ergeben sich Vorteile. Weitere Details finden Sie weiter unten in diesem Artikel.

![Abbildung mit einem potenziellen Szenario für die zukünftige SAP HANA-Sicherung](media/sap-hana-backup-guide/image003.png)

In dieser Abbildung ist ein potenzielles Szenario für eine zukünftige SAP HANA-Sicherung dargestellt. Wenn für SAP HANA das Erstellen von Sicherungen über den Sekundärbereich einer Replikation möglich wäre, wären zusätzliche Optionen für Sicherungsstrategien vorhanden. Dies ist laut einem Beitrag im SAP HANA-Wiki nicht möglich:

_&quot;Ist es möglich, Sicherungen im Sekundärbereich zu erstellen?_

_Nein. Derzeit können Sie Sicherungen von Daten und Protokollen nur im Primärbereich erstellen. Wenn die automatische Sicherung von Protokollen aktiviert ist, werden die Protokollsicherungen nach der Übernahme in den Sekundärbereich dort automatisch geschrieben.&quot;_

## <a name="sap-resources-for-hana-backup"></a>SAP-Ressourcen für die HANA-Sicherung

### <a name="sap-hana-backup-documentation"></a>Dokumentation zur SAP HANA-Sicherung

- [Introduction to SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US) (Einführung in die SAP HANA-Administration)
- [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie)
- [Schedule HANA Backup using ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html) (Planen der HANA-Sicherung mit ABAP DBACOCKPIT)
- [Schedule Data Backups (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm) (Planen von Datensicherungen(SAP HANA Cockpit))
- Häufig gestellte Fragen zur SAP HANA-Sicherung im [SAP-Hinweis 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Häufig gestellte Fragen zur SAP HANA-Datenbank und zu Speichermomentaufnahmen im [SAP-Hinweis 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Ungeeignete Netzwerkdateisysteme für Sicherung und Wiederherstellung im [SAP-Hinweis 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Gründe für SAP HANA-Sicherungen

Bei Azure-Speicher erhalten Sie standardmäßig Verfügbarkeit und Zuverlässigkeit (weitere Informationen zum Azure-Speicher unter [Einführung in Microsoft Azure Storage](../../../storage/common/storage-introduction.md)).

Die Mindestvoraussetzung für die &quot;Sicherung&quot; ist die Verwendung der Azure-SLAs und die Aufbewahrung der SAP HANA-Daten- und -Protokolldateien auf Azure VHDs, die an die SAP HANA-Server-VM angefügt sind. Bei diesem Ansatz sind VM-Ausfälle abgedeckt, aber keine potenziellen Beschädigungen der SAP HANA-Daten- und -Protokolldateien oder logische Fehler wie das versehentliche Löschen von Daten oder Dateien. Sicherungen sind auch aus Konformitäts- oder Rechtsgründen erforderlich. Kurz gesagt: Für SAP HANA-Sicherungen besteht immer eine Notwendigkeit.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sicherstellen der Korrektheit von SAP HANA-Sicherungen
Bei Verwendung von Speichermomentaufnahmen wird die Durchführung einer Testwiederherstellung auf einem anderen System empfohlen. Bei diesem Ansatz kann sichergestellt werden, dass eine Sicherung korrekt ist und die internen Prozesse für die Sicherung und Wiederherstellung wie erwartet funktionieren. Dies stellt im lokalen Bereich eine größere Herausforderung dar, ist aber in der Cloud deutlich einfacher erreichbar, indem die benötigten Ressourcen zu diesem Zweck vorübergehend bereitgestellt werden.

Beachten Sie, dass es nicht ausreicht, eine einfache Wiederherstellung durchzuführen und zu überprüfen, ob HANA betriebsbereit ist. Im Idealfall sollten Sie eine Überprüfung auf die Konsistenz von Tabellen durchführen, um sich zu vergewissern, dass die wiederhergestellte Datenbank fehlerfrei ist. SAP HANA verfügt über mehrere Arten von Konsistenzprüfungen, die im [SAP-Hinweis 1977584](https://launchpad.support.sap.com/#/notes/1977584) beschrieben werden.

Informationen zur Überprüfung der Konsistenz von Tabellen finden Sie außerdem auf der SAP-Website unter [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Konsistenzprüfungen für Tabellen und Kataloge).

Für Standarddateisicherungen ist eine Testwiederherstellung nicht erforderlich. Es gibt zwei SAP HANA-Tools, mit denen Sie überprüfen können, welche Sicherung für die Wiederherstellung verwendet werden kann: hdbbackupdiag und hdbbackupcheck. Weitere Informationen zu diesen Tools finden Sie unter [Manually Checking Whether a Recovery is Possible](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) (Manuelles Überprüfen, ob eine Wiederherstellung möglich ist).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Vor- und Nachteile einer HANA-Sicherung gegenüber einer Speichermomentaufnahme

Im SAP-System besteht keine Präferenz in Bezug auf die HANA-Sicherung bzw. die Speichermomentaufnahme. Die Vor- und Nachteile werden aufgeführt, damit Benutzer je nach Situation und verfügbarer Speichertechnologie ermitteln können, welches Verfahren am besten geeignet ist (siehe [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie)).

Beachten Sie in Azure, dass bei der Azure-Blob-Momentaufnahmenfunktion keine Konsistenz des Dateisystems garantiert ist (siehe [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Verwenden von Blob-Momentaufnahmen mit PowerShell)). Im nächsten Abschnitt (_Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen_) werden einige Aspekte dieser Funktion beschrieben.

Darüber hinaus muss ein Verständnis der Abrechnungsaspekte vorhanden sein, wenn häufig mit Blob-Momentaufnahmen gearbeitet wird, wie in diesem Artikel beschrieben: [Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) (Grundlegendes zu anfallenden Kosten für Momentaufnahmen) – das ist nicht so selbsterklärend wie bei virtuellen Azure-Datenträgern.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen

Die Konsistenz von Dateisystemen und Anwendungen ist beim Erstellen von Momentaufnahmen ein komplexes Problem. Die einfachste Möglichkeit zur Verhinderung von Problemen ist das Herunterfahren von SAP HANA oder sogar des gesamten virtuellen Computers. Das Herunterfahren ist vielleicht für eine Demonstration oder einen Prototyp – oder ggf. auch ein Entwicklungssystem – machbar, aber es ist keine Option für ein Produktionssystem.

Für Azure sollte bedacht werden, dass bei der Azure-Funktion zum Erstellen von Blob-Momentaufnahmen keine Konsistenz des Dateisystems garantiert wird. Unter Verwendung der SAP HANA-Momentaufnahmenfunktion funktioniert dies aber, solange nur ein virtueller Datenträger beteiligt ist. Aber auch bei nur einem Datenträger müssen zusätzliche Elemente überprüft werden. Der [SAP-Hinweis 2039883](https://launchpad.support.sap.com/#/notes/2039883) enthält wichtige Informationen zu SAP HANA-Sicherungen mithilfe von Speichermomentaufnahmen. Beispielsweise wird erwähnt, dass es beim XFS-Dateisystem erforderlich ist, **xfs\_freeze** auszuführen, bevor eine Speichermomentaufnahme gestartet wird, um die Konsistenz sicherzustellen (Details zu **xfs\_freeze** finden Sie unter „[xfs\_freeze(8) – Linux man page](https://linux.die.net/man/8/xfs_freeze)“).

Das Thema Konsistenz wird noch komplexer, wenn ein Dateisystem mehrere Datenträger bzw. Volumes umfasst. Beispielsweise bei Verwendung von mdadm oder LVM und Striping. Der oben erwähnte SAP-Hinweis enthält die folgenden Informationen:

_&quot;Beachten Sie aber, dass über das Speichersystem die E/A-Konsistenz sichergestellt werden muss, wenn eine Speichermomentaufnahme pro SAP HANA-Datenvolume erstellt wird. Die Erstellung von Momentaufnahmen für ein Datenvolume, das spezifisch für den SAP HANA-Dienst ist, muss also ein atomischer Vorgang sein.&quot;_

Angenommen, ein XFS-Dateisystem umfasst vier virtuelle Azure-Datenträger. Mit den folgenden Schritten wird eine konsistente Momentaufnahme bereitgestellt, die den HANA-Datenbereich darstellt:

- Vorbereiten der HANA-Momentaufnahme
- Einfrieren des Dateisystems (z.B. per **xfs\_freeze**)
- Erstellen aller erforderlichen Blob-Momentaufnahmen in Azure
- Aufheben des Einfrierens des Dateisystems
- Bestätigen der HANA-Momentaufnahme

Die Empfehlung besteht darin, das obige Verfahren unabhängig vom Dateisystem sicherheitshalber in allen Fällen zu verwenden. Oder wenn es sich um einen Einzeldatenträger oder Striping mit mdadm oder LVM über mehrere Datenträger handelt.

Es ist wichtig, die HANA-Momentaufnahme zu bestätigen. Aufgrund des &quot;Copy-on-Write&quot;-Vorgangs ist für SAP HANA in diesem Vorbereitungsmodus für Momentaufnahmen ggf. kein zusätzlicher Datenträger-Speicherplatz erforderlich. Das Starten neuer Sicherungen ist ebenfalls erst möglich, nachdem die SAP HANA-Momentaufnahme bestätigt wurde.

Für den Azure Backup-Dienst werden Azure-VM-Erweiterungen verwendet, um für die Konsistenz des Dateisystems zu sorgen. Diese VM-Erweiterungen sind für die alleinige Nutzung nicht verfügbar. Die SAP HANA-Konsistenz muss trotzdem verwaltet werden. Weitere Informationen finden Sie im verwandten Artikel [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md).

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategie für die Zeitplanung für SAP HANA-Sicherungen

Der SAP HANA-Artikel [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie) enthält einen einfachen Plan zur Durchführung von Sicherungen:

- Speichermomentaufnahme (täglich)
- Vollständige Datensicherung im Datei- oder Bacint-Format (einmal pro Woche)
- Automatische Sicherungen von Protokollen

Sie können auch vollständig auf Speichermomentaufnahmen verzichten. Diese können durch HANA-Deltasicherungen ersetzt werden, z.B. inkrementelle oder differenzielle Sicherungen (siehe [Delta Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Deltasicherungen)).

Der Leitfaden zur HANA-Administration enthält eine Liste mit Beispielen. Es wird vorgeschlagen, dass anhand der folgenden Sicherungssequenz der SAP HANA-Stand eines bestimmten Zeitpunkts wiederhergestellt wird:

1. Vollständige Datensicherung
2. Differenzielle Sicherung
3. Inkrementelle Sicherung 1
4. Inkrementelle Sicherung 2
5. Protokollsicherungen

Im Hinblick auf einen genauen Zeitplan, mit dem Zeitpunkt und Häufigkeit eines bestimmten Typs von Sicherung bestimmt werden, kann keine allgemeine Richtlinie aufgestellt werden. Dies ist kundenspezifisch und hängt davon ab, wie viele Datenänderungen im System vorgenommen werden. Eine grundlegende Empfehlung von SAP, die als allgemeingültige Richtlinie angesehen werden kann, ist die Erstellung einer vollständigen HANA-Sicherung pro Woche.
Informationen zu Protokollsicherungen finden Sie in der SAP HANA-Dokumentation zu [Log Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Protokollsicherungen).

Darüber hinaus empfiehlt SAP eine Überprüfung des Sicherungskatalogs, um zu verhindern, dass dieser sehr umfangreich wird (siehe [Housekeeping for Backup Catalog and Backup Storage](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm) (Housekeeping für Sicherungskatalog und Sicherungsspeicher)).

### <a name="sap-hana-configuration-files"></a>SAP HANA-Konfigurationsdateien

Wie in den häufig gestellten Fragen im [SAP-Hinweis 1642148](https://launchpad.support.sap.com/#/notes/1642148) beschrieben, sind die SAP HANA-Konfigurationsdateien nicht Teil einer HANA-Standardsicherung. Diese Dateien sind für die Wiederherstellung eines Systems nicht unbedingt erforderlich. Die HANA-Konfiguration kann nach der Wiederherstellung manuell geändert werden. Falls Sie die gleiche benutzerdefinierte Konfiguration während des Wiederherstellungsprozesses erhalten möchten, müssen Sie die HANA-Konfigurationsdateien separat sichern.

Falls HANA-Standardsicherungen in einem dedizierten HANA-Sicherungsdateisystem erstellt werden, können die Konfigurationsdateien auch in dasselbe Sicherungsdateisystem kopiert werden, und anschließend können alle Daten zusammen auf das endgültige Speicherziel kopiert werden, z.B. einen „kalten“ Blobspeicher.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit ermöglicht die Überwachung und Verwaltung von SAP HANA per Browser. Die Anwendung ermöglicht die Behandlung von SAP HANA-Sicherungen, sodass sie als Alternative zu SAP HANA Studio und ABAP DBACOCKPIT eingesetzt werden kann (weitere Informationen unter [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm)).

![Abbildung: SAP HANA Cockpit-Bildschirm für die Datenbankverwaltung](media/sap-hana-backup-guide/image004.png)

In dieser Abbildung sind der SAP HANA-Bildschirm für die Datenbankverwaltung und links die Kachel für die Sicherung dargestellt. Die Kachel für die Sicherung wird angezeigt, wenn der Benutzer für sein Anmeldekonto über die erforderlichen Berechtigungen verfügt.

![Sicherungen können in SAP HANA Cockpit verfolgt werden, während sie durchgeführt werden](media/sap-hana-backup-guide/image005.png)

Sicherungen können in SAP HANA Cockpit verfolgt werden, während sie durchgeführt werden, und nach Abschluss des Vorgangs sind alle Sicherungsdetails verfügbar.

![Beispiel: Verwendung von Firefox auf einer Azure SLES 12-VM mit Gnome-Desktop](media/sap-hana-backup-guide/image006.png)

Die obigen Screenshots wurden auf einer Azure-Windows-VM erstellt. Dies ist ein Beispiel für die Verwendung von Firefox auf einer Azure SLES 12-VM mit Gnome-Desktop. Sie sehen die Option zum Definieren von SAP HANA-Sicherungszeitplänen in SAP HANA Cockpit. Außerdem ist erkennbar, dass als Präfix für die Sicherungsdateien Datum und Uhrzeit vorgeschlagen werden. In SAP HANA Studio lautet das Standardpräfix &quot;COMPLETE\_DATA\_BACKUP&quot;, wenn eine vollständige Dateisicherung durchgeführt wird. Die Verwendung eines eindeutigen Präfix ist zu empfehlen.

### <a name="sap-hana-backup-encryption"></a>SAP HANA-Sicherungsverschlüsselung

SAP HANA ermöglicht die Verschlüsselung von Daten und Protokollen. Wenn SAP HANA-Daten und -Protokolle nicht verschlüsselt werden, sind auch die Sicherungen nicht verschlüsselt. Der Kunde kann eine Drittanbieterlösung verwenden, um die SAP HANA-Sicherungen zu verschlüsseln. Weitere Informationen zur SAP HANA-Verschlüsselung finden Sie unter [Data and Log Volume Encryption](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) (Verschlüsselung von Daten- und Protokollvolumes).

In Microsoft Azure kann ein Kunde zum Verschlüsseln die IaaS-VM-Verschlüsselungsfunktion verwenden. Beispielsweise können dedizierte Datenträger verwendet werden, die an die VM angefügt sind. Sie werden zum Speichern von SAP HANA-Sicherungen genutzt, und anschließend werden Kopien dieser Datenträger erstellt.

Der Azure Backup-Dienst kann verschlüsselte VMs/Datenträger verarbeiten (siehe [Sichern und Wiederherstellen verschlüsselter virtueller Computer mit Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Eine weitere Möglichkeit ist die Verwendung der SAP HANA-VM und ihrer Datenträger ohne Verschlüsselung und die Speicherung der SAP HANA-Sicherungsdateien in einem Speicherkonto, für das die Verschlüsselung aktiviert wurde (siehe [Azure Storage Service Encryption für ruhende Daten](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Testeinrichtung

### <a name="test-virtual-machine-on-azure"></a>Testen des virtuellen Computers in Azure

Für die folgenden Sicherungs- und Wiederherstellungstests wurde eine SAP HANA-Installation in einer Azure GS5-VM verwendet. Für VMs der M-Serie gilt das gleiche Prinzip.

![Abbildung: Ausschnitt aus der Übersicht im Azure-Portal für die HANA-Test-VM](media/sap-hana-backup-guide/image007.png)

Diese Abbildung enthält einen Ausschnitt aus der Übersicht im Azure-Portal für die HANA-Test-VM.

### <a name="test-backup-size"></a>Testen der Sicherungsgröße

![Abbildung wurde unter Verwendung der Sicherungskonsole in HANA Studio erstellt und zeigt die Sicherungsdateigröße von 229 GB für den HANA-Indexserver](media/sap-hana-backup-guide/image008.png)

Eine Testtabelle wurde mit Daten gefüllt, um für die Datensicherung eine Gesamtgröße von über 200 GB zu erzielen und realistische Leistungsdaten zu erhalten. Diese Abbildung wurde unter Verwendung der Sicherungskonsole in HANA Studio erstellt und zeigt die Sicherungsdateigröße von 229 GB für den HANA-Indexserver. Für die Tests wurde das Standardpräfix „COMPLETE_DATA_BACKUP“ für Sicherungen in SAP HANA Studio verwendet. In echten Produktionssystemen sollte ein nützlicheres Präfix festgelegt werden. In SAP HANA Cockpit wird Datum/Uhrzeit vorgeschlagen.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testtool zum direkten Kopieren von Dateien in den Azure-Speicher

Zum direkten Übertragen von SAP HANA-Sicherungsdateien in Azure Blob Storage oder auf Azure-Dateifreigaben wurde das Tool blobxfer verwendet. Es unterstützt beide Ziele und kann aufgrund seiner Befehlszeilenschnittstelle leicht in Automatisierungsskripts integriert werden. Das Tool blobxfer ist auf [GitHub](https://github.com/Azure/blobxfer) verfügbar.

### <a name="test-backup-size-estimation"></a>Testen der Schätzung der Sicherungsgröße

Es ist wichtig, die Größe der Sicherungen von SAP HANA zu schätzen. Mit dieser Schätzung wird die Leistung verbessert, indem die maximale Sicherungsdateigröße für eine Reihe von Sicherungsdateien definiert wird. Der Grund hierfür ist die Parallelität während eines Kopiervorgangs für Dateien. (Dies wird weiter unten in diesem Artikel näher erläutert.) Sie müssen auch entscheiden, ob eine vollständige Sicherung oder eine Deltasicherung (inkrementell oder differenziell) durchgeführt werden soll.

Glücklicherweise gibt es eine einfache SQL-Anweisung, mit der die Größe von Sicherungsdateien geschätzt werden kann: **select \* from M\_BACKUP\_SIZE\_ESTIMATIONS** (siehe [Estimate the Space Needed in the File System for a Data Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm) (Schätzen des Speicherplatzes, der im Dateisystem für eine Datensicherung benötigt wird)).

![Ausgabe dieser SQL-Anweisung stimmt fast genau mit der tatsächlichen Größe der vollständigen Datensicherung auf dem Datenträger überein](media/sap-hana-backup-guide/image009.png)

Für das Testsystem stimmt die Ausgabe dieser SQL-Anweisung fast genau mit der tatsächlichen Größe der vollständigen Datensicherung auf dem Datenträger überein.

### <a name="test-hana-backup-file-size"></a>Testen der Größe der HANA-Sicherungsdatei

![Über die Sicherungskonsole von HANA Studio können Sie die maximal zulässige Größe für HANA-Sicherungsdateien festlegen](media/sap-hana-backup-guide/image010.png)

Über die Sicherungskonsole von HANA Studio können Sie die maximal zulässige Größe für HANA-Sicherungsdateien festlegen. In der Beispielumgebung ermöglicht es diese Funktion, dass Sie anstelle einer Sicherungsdatei mit 230 GB mehrere kleine Sicherungsdateien erhalten. Eine geringere Dateigröße hat eine erhebliche Auswirkung auf die Leistung (siehe verwandten Artikel [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Zusammenfassung

Basierend auf den Testergebnissen sind in den folgenden Tabellen die Vor- und Nachteile von Lösungen aufgeführt, die zum Sichern einer auf Azure-VMs ausgeführten SAP HANA-Datenbank verwendet werden.

**Sichern von SAP HANA im Dateisystem und anschließendes Kopieren von Sicherungsdateien auf das endgültige Sicherungsziel**

|Lösung                                           |Vorteile                                 |Nachteile                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Aufbewahren von HANA-Sicherungen auf VM-Datenträgern                      |Kein zusätzlicher Verwaltungsaufwand     |Belegt lokalen VM-Datenträger-Speicherplatz           |
|Tool blobxfer zum Kopieren von Sicherungsdateien in Blobspeicher |Parallelität beim Kopieren von mehreren Dateien, Nutzung von „kaltem“ Blobspeicher möglich | Zusätzlicher Wartungsaufwand für das Tool und benutzerdefinierte Skripterstellung | 
|Blob-Kopiervorgang per PowerShell oder CLI                    |Kein zusätzliches Tool erforderlich, Einsatz von Azure PowerShell oder CLI möglich |Manueller Prozess, Kunde muss sich um Skripterstellung und Verwaltung von kopierten Blobs für die Wiederherstellung kümmern|
|Kopieren auf NFS-Freigabe                                  |Nachverarbeitung von Sicherungsdateien auf einer anderen VM ohne Auswirkung auf den HANA-Server|Langsamer Kopiervorgang|
|blobxfer-Kopiervorgang für Azure-Dateidienst                |Belegt keinen Speicherplatz auf lokalen VM-Datenträgern|Keine direkte Schreibunterstützung durch HANA-Sicherung, derzeitige Größenbeschränkung der Dateifreigabe von 5 TB|
|Azure Backup-Agent                                 | Bevorzugte Lösung         | Derzeit nicht unter Linux verfügbar    |



**SAP HANA-Sicherung basierend auf Speichermomentaufnahmen**

|Lösung                                           |Vorteile                                 |Nachteile                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup-Dienst                               | VM-Sicherung basierend auf Blob-Momentaufnahmen | Wenn die Wiederherstellung auf Dateiebene nicht verwendet wird, ist die Erstellung einer neuen VM für den Wiederherstellungsprozess erforderlich, wofür ein neuer SAP HANA-Lizenzschlüssel benötigt wird|
|Manuelle Blob-Momentaufnahmen                              | Flexible Erstellung und Wiederherstellung von bestimmten VM-Datenträgern ohne Änderung der eindeutigen VM-ID|Manuelle Schritte, die vom Kunden durchgeführt werden müssen|

## <a name="next-steps"></a>Nächste Schritte
* Unter [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md) wird die dateibasierte Sicherung beschrieben.
* In [SAP HANA backup based on storage snapshots](sap-hana-backup-storage-snapshots.md) (SAP HANA-Sicherung auf der Grundlage von Speichermomentaufnahmen) wird die auf Speichermomentaufnahmen basierende Sicherungsoption behandelt.
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
